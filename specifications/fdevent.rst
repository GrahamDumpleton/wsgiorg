Waiting for File Descriptor Events
==================================

:Title: Waiting for File Descriptor Events
:Author: Christopher Stawarz <chris@pseudogreen.org>
:Discussions-To: Python Web-SIG <web-sig@python.org>
:Status: Proposed
:Created: 11-May-2008

.. contents::

Abstract
--------

This specification defines a set of extensions that allow a WSGI
application to suspend its execution until an event occurs on a
specified file descriptor.

Rationale
---------

The architecture of asynchronous (aka event driven) servers requires
all I/O operations, including both interprocess and network
communication, to be non-blocking.  For a WSGI-compliant server, this
requirement extends to all applications run on the server.  However,
the WSGI specification does not provide sufficient facilities for an
application to ensure that its I/O is non-blocking.  Specifically, it
lacks a mechanism by which an application can suspend its execution
until an arbitrary file descriptor (such as one belonging to a socket
or pipe opened by the application) is ready for reading or writing.
This specification defines a standard interface by which servers can
provide such a mechanism to applications.

Specification
-------------

This specification introduces three new variables to the WSGI
environment: :envvar:`x-wsgiorg.fdevent.readable`,
:envvar:`x-wsgiorg.fdevent.writable`, and
:envvar:`x-wsgiorg.fdevent.timeout`.

The variables :envvar:`x-wsgiorg.fdevent.readable` and
:envvar:`x-wsgiorg.fdevent.writable` are callable objects that accept
two positional arguments, one required and one optional.  In the
following description, these arguments are given the names ``fd`` and
``timeout``, but they are not required to have these names, and the
application **must** invoke the callables using positional arguments.

The first argument, ``fd``, is either an integer representing a file
descriptor or an object with a ``fileno`` method that returns such an
integer.  The set of acceptable file descriptors is defined to be
those accepted by ``select.select``.  (Note that this set is platform
dependent: only sockets are allowed on Windows, whereas sockets,
pipes, and files are acceptable on Unix-like systems.)  The second,
optional argument, ``timeout``, is either ``None`` or a floating-point
value in seconds.  If omitted, it defaults to ``None``.

When called, :envvar:`x-wsgiorg.fdevent.readable` and
:envvar:`x-wsgiorg.fdevent.writable` return the empty string (``''``),
which **must** be yielded by the application iterable to the server.
(The result of calling :envvar:`x-wsgiorg.fdevent.readable` or
:envvar:`x-wsgiorg.fdevent.writable` and yielding a non-empty string,
or making multiple calls to :envvar:`x-wsgiorg.fdevent.readable`
and/or :envvar:`x-wsgiorg.fdevent.writable` before yielding the empty
string, is undefined.)  The server then suspends execution of the
application until one of the following conditions is met:

* The specified file descriptor is ready for reading (if the
  application called :envvar:`x-wsgiorg.fdevent.readable`) or writing
  (if the application called :envvar:`x-wsgiorg.fdevent.writable`).

* ``timeout`` seconds have elapsed without the desired file descriptor
  becoming readable (if the application called
  :envvar:`x-wsgiorg.fdevent.readable`) or writable (if the
  application called :envvar:`x-wsgiorg.fdevent.writable`), unless the
  value of ``timeout`` is ``None``, in which case the wait will never
  timeout.

* The server detects an error or "exceptional" condition (such as
  out-of-band data) on the file descriptor.

Put another way, if the application calls
:envvar:`x-wsgiorg.fdevent.readable` and yields the empty string, it
will be suspended until ``select.select([fd],[],[fd],timeout)`` would
return.  If the application calls :envvar:`x-wsgiorg.fdevent.writable`
and yields the empty string, it will be suspended until
``select.select([],[fd],[fd],timeout)`` would return.

The variable :envvar:`x-wsgiorg.fdevent.timeout` is an object whose
truth value can be changed by the server.  (For example, it could be a
``list`` instance, whose truth value is false when empty, true
otherwise.)  If ``timeout`` seconds elapse without the desired file
descriptor event occurring, :envvar:`x-wsgiorg.fdevent.timeout` will
be true when the application resumes; otherwise, it will be false.
The truth value of :envvar:`x-wsgiorg.fdevent.timeout` when the
application is first started or after it yields each response-body
string is undefined.

The server may use any technique it desires to detect events on an
application's file descriptors.  (Most likely, it will add them to the
same event loop that it uses for accepting new client connections,
receiving requests, and sending responses.)

Handling of the Input Stream
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

While technically outside the scope of this specification, the
application's input stream (:envvar:`wsgi.input`) is another
source of potentially blocking I/O that deserves mention.

The methods provided by the input stream follow the semantics of the
corresponding methods of the ``file`` class.  In particular, each of
these methods can invoke the underlying I/O function (in this case,
``recv`` on the socket connected to the client) more than once,
without giving the application the opportunity to check whether each
invocation will block.  Although authors of asynchronous servers may
be tempted to provide a non-standard input stream that supports
on-demand, non-blocking reads, such an input stream would be
incompatible with WSGI middleware.

In order to avoid these problems, it is strongly recommended that
asynchronous servers pre-read the entire request body (to an in-memory
buffer or temporary file) before invoking the application, either by
default or as a configurable option.  Doing so will ensure that the
input stream is compatible with middleware and that reads from it will
not block waiting for data from the client.

Examples
--------

The following application acts as a proxy to `python.org
<http://python.org>`_.  It uses a :class:`pycurl.CurlMulti` instance
to perform the outgoing HTTP request in a non-blocking fashion.  When
the :meth:`CurlMulti.perform` method detects that its next I/O
operation would block, it returns control to the application, which
then yields until the file descriptor of interest becomes readable or
writable as required.  If the descriptor is not ready after one
second, the application sends a ``504 Gateway Timeout`` response to
the client and terminates::

    def pyorg_proxy(environ, start_response):
        result = StringIO()

        c = pycurl.Curl()
        c.setopt(pycurl.URL, 'http://python.org' + environ['PATH_INFO'])
        c.setopt(pycurl.WRITEFUNCTION, result.write)

        m = pycurl.CurlMulti()
        m.add_handle(c)

        while True:
            while True:
                ret, num_handles = m.perform()
                if ret != pycurl.E_CALL_MULTI_PERFORM:
                    break
            if not num_handles:
                break

            read, write, exc = m.fdset()
            if read:
                yield environ['x-wsgiorg.fdevent.readable'](read[0], 1.0)
            else:
                yield environ['x-wsgiorg.fdevent.writable'](write[0], 1.0)

            if environ['x-wsgiorg.fdevent.timeout']:
                msg = 'The request to python.org timed out.'
                start_response('504 Gateway Timeout',
                               [('Content-Type', 'text/plain'),
                                ('Content-Length', str(len(msg)))])
                yield msg
                return

        start_response('200 OK', [('Content-Type', 'application/octet-stream'),
                                  ('Content-Length', str(result.len))])
        yield result.getvalue()

The following adapter allows an application that uses the
:envvar:`x-wsgiorg.fdevent` extensions to run on a server that does not
support them, without any modification to the application's code::

    def with_fdevent(application):
        def wrapper(environ, start_response):
            select_args = [None]

            def readable(fd, timeout=None):
                assert (not select_args[0])
                select_args[0] = ([fd], [], [fd], timeout)
                return ''

            def writable(fd, timeout=None):
                assert (not select_args[0])
                select_args[0] = ([], [fd], [fd], timeout)
                return ''

            environ['x-wsgiorg.fdevent.readable'] = readable
            environ['x-wsgiorg.fdevent.writable'] = writable

            timeout = False

            class TimeoutWrapper(object):
                def __nonzero__(self):
                    return timeout

            environ['x-wsgiorg.fdevent.timeout'] = TimeoutWrapper()

            for result in application(environ, start_response):
                assert (not (result and select_args[0]))
                if result or (not select_args[0]):
                    yield result
                else:
                    ready = select.select(*select_args[0])
                    timeout = (ready == ([], [], []))
                    select_args[0] = None

        return wrapper

Problems
--------

* The empty string yielded by an application after calling
  :envvar:`x-wsgiorg.fdevent.readable` or
  :envvar:`x-wsgiorg.fdevent.writable` must pass through any
  intervening middleware and be detected by the server.  Although WSGI
  explicitly requires middleware to relay such strings to the server
  (see `Middleware Handling of Block Boundaries
  <http://python.org/dev/peps/pep-0333/#middleware-handling-of-block-boundaries>`_),
  some components may not, making them incompatible with this
  specification.

Other Possibilities
-------------------

* To prevent an application that does blocking I/O from blocking the
  entire server, an asynchronous server could run each instance of the
  application in a separate thread.  However, since asynchronous
  servers achieve high levels of concurrency by expressly *avoiding*
  multithreading, this technique will almost always be unacceptable.

* The `greenlet <http://codespeak.net/py/dist/greenlet.html>`_ package
  enables the use of cooperatively-scheduled micro-threads in Python
  programs, and a WSGI server could potentially use it to pause and
  resume applications around blocking I/O operations.  However, such
  micro-threading is not part of the Python language or standard
  library, and some server authors may be unwilling or unable to make
  use of it.

Open Issues
-----------

* Some third-party libraries (such as `PycURL
  <http://pycurl.sourceforge.net/>`_) provide non-blocking interfaces
  that may need to monitor multiple file descriptors for events
  simultaneously.  Since this specification allows an application to
  wait on only one file descriptor at a time, application authors may
  find it difficult or impossible to use such libraries, or they may
  be limited to a subset of the libraries' capabilities.

  Although this specification could be extended to include an
  interface for waiting on multiple file descriptors, it is unclear
  whether it would be easy (or even possible) for all servers to
  implement it.  Also, the appropriate behavior for a multi-descriptor
  wait is not obvious.  (Should the application be resumed when a
  single descriptor is ready?  All of them?  Some minimum number?)
