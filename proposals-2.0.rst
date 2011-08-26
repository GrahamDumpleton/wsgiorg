Proposals related to WSGI 2.0
=============================

This page is intended to collect any ideas related to WSGI 2.0. In
particular, any proposed changes to the specification.

NOTE: What is described here should not be considered a DRAFT for WSGI
2.0. It is only a list of ideas or issues that need to be considered
if there ever is enough momentum towards producing an updated WSGI
specification. It is quite possible that there may never be an updated
specification which embodies the ideas described here. Thus, if you
implement any web application interfaces based on the API described
here, call it something else, do not call it WSGI 2.0 as no such thing
exists.

start_response and write
------------------------

We could remove start_response and the writer that it implies. This
would lead to a signature like:

.. code-block:: python

    def app(environ):
        return '200 OK', [('Content-type', 'text/plain')], ['Hello world']

That is, return a three-tuple of ``(status, headers, app_iter)``.

It's relatively simple to provide adapters to and from this signature
to the WSGI 1.0 signature.

Making some keys required
-------------------------

Several keys are optional in WSGI, but required in CGI, in particular
``SCRIPT_NAME``, ``PATH_INFO`` and ``QUERY_STRING``. Also
``REMOTE_ADDR`` and ``SERVER_SOFTWARE`` are supposed to exist, even if
empty. All these keys could become required in WSGI.

Unknown-length wsgi.input
-------------------------

There's no documented way to indicate that there is content in
``environ['wsgi.input']``, but the content length is unknown. A value
of ``-1`` may work in many situations. A missing ``CONTENT_LENGTH``
doesn't generally work currently (it's assumed to mean 0 by much
code).

This is an issue because chunked transfer encoding on request content
can't be supported properly unless there is a way to indicate that
there is data with unknown content length. Also an issue with a web
server or WSGI middleware component that mutates the input stream
(eg. decompression), where it will not know the new content length in
advance of mutating the data stream.

Any change in this area also needs to take into consideration the
current link between CGI and WSGI specifications and whether the CGI
requirement to not read more input data than defined by
``CONTENT_LENGTH`` and that returning an EOF indicator is optional is
really appropriate for WSGI.

For more information see thread:
http://mail.python.org/pipermail/web-sig/2007-March/002630.html

readline(size)
--------------

Currently the specification does not require servers to provide
``environ['wsgi.input'].readline(size)`` (the size argument in
particular). But ``cgi.FieldStorage`` calls readline this way, so in
effect it is required.

app_iter and threads
--------------------

It's not clear if the app_iter must be used in the same thread as the
application. Since the application is blocking, presumably it must be
run all in one thread. This should be more explicitly documented.

long response headers
---------------------

Noted here:
http://mail.python.org/pipermail/web-sig/2006-September/002244.html

request trailers and chunked transfer encoding
----------------------------------------------

When using chunked transfer encoding on request content, the RFCs
allow there to be request trailers. These are like request headers but
come after the final null data chunk. These trailers are only
available when the chunked data stream is finite length and when it
has all been read in, thus not available at time that start
application is called.

Decoding SCRIPT_NAME/PATH_INFO
------------------------------

Because ``SCRIPT_NAME`` and ``PATH_INFO`` are decoded in WSGI, there's
no way to distinguish ``%2F`` from ``/``

No encoding horrors any more
----------------------------

Analysis see there:
http://www.mail-archive.com/web-sig@python.org/msg02483.html

Can we have that horror removed for wsgi2 apps, please?

A quite easy approach would be to have a set of ``RAW_*`` env vars
(e.g. ``RAW_PATH_INFO``) that has ``/Foo%XXBar%YY`` content (is not
decoded, plain ascii like in the http protocol).

That also would solve issues with ``?`` and ``/`` (see section above)
that are encoded as ``%XX`` (and NOT meant as query / path component
separator).

Any wsgi1 app can continue to use the wsgi1 env vars, any wsgi2 app
can check whether the wsgi2 ``RAW_*`` env vars are there and use them
(or fall back to using the wsgi1 env vars).
