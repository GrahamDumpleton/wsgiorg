Amendments to WSGI 1.0
======================

This page is intended to collect any ideas related to amendments to
the original `WSGI 1.0 <http://www.python.org/dev/peps/pep-0333/>`_ so
that it can be marked as 'Final'.

The purpose of the amendments is to address any mistakes or
ambiguities in the 1.0 specification or to change any requirements
that in practice could not be implemented for one reason or
another. The amendments would also address any differences in how the
1.0 specification should be interpreted for Python 3. See
:doc:`python3` for details.

Note that this isn't about changing the 1.0 specification drastically
in any way, that is what :doc:`proposals-2.0` specification will be
about. You should though not construe anything in here as an
indication that said change will be made. This is especially the case
with Python 3 support as there is a measure of disagreement as to how
WSGI should work for Python 3. In other words, you would be unwise to
implement any WSGI application or WSGI adapter with information in
here as a basis as it could change or simply never be adopted.

The page has been created in response to `a discussion on the Python
WEB-SIG
<http://groups.google.com/group/python-web-sig/browse_frm/thread/ae4bf2f41ed10350>`_.

In addition, Graham Dumpleton gives `details and clarifications on
WSGI 1.0 amendments
<http://blog.dscpl.com.au/2009/10/details-on-wsgi-10-amendmentsclarificat.html>`_
on his blog.

readline(size)
--------------

Currently the specification does not require servers to provide
``environ['wsgi.input'].readline(size)`` (the ``size`` argument in
particular). But :py:class:`cgi.FieldStorage` calls readline this way,
so in effect it is required.

Python 3
--------

:doc:`python3` default string type is now unicode and existing python2
strings correspond to bytes. This changes how terms need to be
interpreted. From `WSGI, Python 3 and Unicode
<http://groups.google.com/group/python-web-sig/browse_frm/thread/f8f54fe99485312a/046841da888eac1e#046841da888eac1e>`_,
the following suggested amendments were proposed for Python 3.

 * When running under Python 3, applications **SHOULD** produce bytes
   output, status line and headers
 * When running under Python 3, servers and gateways **MUST** accept
   strings as application output, status line or headers, under the
   existing rules (i.e., ``s.encode('latin-1')`` must convert the
   string to bytes without an exception)
 * When running under Python 3, servers **MUST** provide CGI HTTP
   variables and as strings, decoded from the headers using HTTP
   standard encodings (i.e. latin-1 + :rfc:`2047`) (Open question: are
   there any CGI or WSGI variables that should NOT be strings?)
 * When running under Python 3, servers **MUST** make ``wsgi.input`` a
   binary (byte) stream
 * When running under Python 3, servers **MUST** provide a text stream
   for ``wsgi.errors``

See the mailing list archive for the full discussion of issues.

Note that this doesn't address any clarifications that may be required
around ``wsgi.file_wrapper`` optional extension.

Note that current thinking is that the WSGI adaptor should not worry
about :rfc:`2047`.

Errata 1
--------

In the "Specification Details" chapter there is this note:

.. note::
    the application must invoke the ``start_response()`` callable
    before the iterable yields its first body string, so that the
    server can send the headers before any body content. However, this
    invocation may be performed by the iterable's first iteration, so
    servers must not assume that ``start_response()`` has been called
    before they begin iterating over the iterable.)  What's wrong is
    that the invocation of start_response may be performed at any
    iteration of the iterable, as long as the application yields empty
    strings.

See http://mail.python.org/pipermail/web-sig/2007-December/003064.html
for more info.

 * I don't really think that this is a good assumption to make.  I
   could see how some implementations could allow for this, but
   strictly speaking, I wouldn't assume that most implementations
   would do that.  Besides that, what purpose does yielding an empty
   string serve?  For those reasons, I think this is better of left as
   an undefined behavior. --JasonBaker July 1, 2008

When HTTP response headers can be sent
--------------------------------------

The WSGI spec explicitly states that HTTP response headers must be
sent when the application yields the first non empty strings.

However if a WSGI implementation is allowed to send headers early
(*not* when ``start_response`` is called, but when the first
string is yielded by the WSGI application, even if empty), then in
case of an ``HEAD`` request no content generation is required
(assuming, of course, that the WSGI application returns a generator).

See http://mail.python.org/pipermail/web-sig/2007-October/002881.html,
http://mail.python.org/pipermail/web-sig/2007-October/002799.html,
http://mail.python.org/pipermail/web-sig/2007-October/002803.html and
http://mail.python.org/pipermail/web-sig/2007-October/002879.html

That thread is a bit confused.

start_response and error checks
-------------------------------

The WSGI spec says that start_response callable **must not** actually
transmit the response headers. Instead, it must store them.

The problem is that it says nothing about errors checking.

See
http://mail.python.org/pipermail/web-sig/2007-September/002771.html

Clarification about start_response
----------------------------------

What happens if an application calls ``start_response`` with an
incorrect status line or headers?

Should an implementation consider the function *called*, so that an
application can call it a second time, *without* the exc_info
parameter?

See http://mail.python.org/pipermail/web-sig/2007-October/002887.html

Specify the type of ``SERVER_PORT``
-----------------------------------

Some implementations currently expect it to be an integer, some a
string.  Can we please specify one or the other or either? The "URL
reconstruction" code snippet in :pep:`333` presumes it's a string, the
reference to the (defunct) CGI spec would seem to imply it should be a
string, but it should be explicit.
