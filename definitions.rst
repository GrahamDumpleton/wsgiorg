Definitions of keys and classes
===============================

Standard ``environ`` keys
-------------------------

.. envvar:: REQUEST_METHOD

    The HTTP request method, such as ``GET`` or ``POST``. This cannot
    ever be an empty string, and so is always required.

.. envvar:: SCRIPT_NAME

    The initial portion of the request URL's "path" that corresponds
    to the application object, so that the application knows its
    virtual "location". This may be an empty string, if the
    application corresponds to the "root" of the server.

.. envvar:: PATH_INFO

    The remainder of the request URL's "path", designating the virtual
    "location" of the request's target within the application. This
    may be an empty string, if the request URL targets the application
    root and does not have a trailing slash.

.. envvar:: QUERY_STRING

    The portion of the request URL that follows the "?", if any. May
    be empty or absent.

.. envvar:: CONTENT_TYPE

    The contents of any ``Content-Type`` fields in the HTTP
    request. May be empty or absent.

.. envvar:: CONTENT_LENGTH

    The contents of any ``Content-Length`` fields in the HTTP
    request. May be empty or absent.

.. envvar:: SERVER_NAME

.. envvar:: SERVER_PORT

    When combined with :envvar:`SCRIPT_NAME` and :envvar:`PATH_INFO`,
    these variables can be used to complete the URL. Note, however,
    that :envvar:`HTTP_HOST`, if present, should be used in preference
    to :envvar:`SERVER_NAME` for reconstructing the request URL. See
    the URL Reconstruction section below for more
    detail. :envvar:`SERVER_NAME` and :envvar:`SERVER_PORT` can never
    be empty strings, and so are always required.

.. envvar:: SERVER_PROTOCOL

    The version of the protocol the client used to send the
    request. Typically this will be something like "HTTP/1.0" or
    "HTTP/1.1" and may be used by the application to determine how to
    treat any HTTP request headers. (This variable should probably be
    called :envvar:`REQUEST_PROTOCOL`, since it denotes the protocol
    used in the request, and is not necessarily the protocol that will
    be used in the server's response. However, for compatibility with
    CGI we have to keep the existing name.)

``HTTP_`` Variables

    Variables corresponding to the client-supplied HTTP request
    headers (i.e., variables whose names begin with ``HTTP_``). The
    presence or absence of these variables should correspond with the
    presence or absence of the appropriate HTTP header in the request.

WSGI ``environ`` keys
---------------------

.. envvar:: wsgi.version

    The tuple (1, 0), representing WSGI version 1.0.

.. envvar:: wsgi.url_scheme

    A string representing the "scheme" portion of the URL at which the
    application is being invoked. Normally, this will have the value
    "http" or "https", as appropriate.

.. envvar:: wsgi.input

    An input stream (file-like object) from which the HTTP request
    body can be read. (The server or gateway may perform reads
    on-demand as requested by the application, or it may pre- read the
    client's request body and buffer it in-memory or on disk, or use
    any other technique for providing such an input stream, according
    to its preference.)

.. envvar:: wsgi.errors

    An output stream (file-like object) to which error output can be
    written, for the purpose of recording program or other errors in a
    standardized and possibly centralized location. This should be a
    "text mode" stream; i.e., applications should use "\n" as a line
    ending, and assume that it will be converted to the correct line
    ending by the server/gateway.

    For many servers, wsgi.errors will be the server's main error
    log. Alternatively, this may be sys.stderr, or a log file of some
    sort. The server's documentation should include an explanation of
    how to configure this or where to find the recorded output. A
    server or gateway may supply different error streams to different
    applications, if this is desired.

.. envvar:: wsgi.multithread

    This value should evaluate true if the application object may be
    simultaneously invoked by another thread in the same process, and
    should evaluate false otherwise.

.. envvar:: wsgi.multiprocess

    This value should evaluate true if an equivalent application
    object may be simultaneously invoked by another process, and
    should evaluate false otherwise.

.. envvar:: wsgi.run_once

    This value should evaluate true if the server or gateway expects
    (but does not guarantee!) that the application will only be
    invoked this one time during the life of its containing
    process. Normally, this will only be true for a gateway based on
    CGI (or something similar).
