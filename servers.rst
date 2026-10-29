Servers which support WSGI
==========================

This is an alphabetic list of WSGI servers.  In some cases these are
WSGI-only systems, in other cases a package includes a server.

Please feel free to expand the list or descriptions.  Direct links to
documentation on how to use the server is especially appreciated.

`Cheroot <https://cheroot.cherrypy.dev>`_
    The high-performance, pure-Python HTTP server used by CherryPy
    (formerly ``cherrypy.wsgiserver``). A production-ready, thread
    pooled, generic WSGI server with SSL support and
    Transfer-Encoding: chunked.  It can run any WSGI application, not
    only CherryPy ones.

`eventlet.wsgi <https://eventlet.readthedocs.io/en/latest/modules/wsgi.html>`_
    A WSGI server built on `eventlet <https://eventlet.readthedocs.io/>`_
    green threads, with SSL support.

`gevent.pywsgi <https://www.gevent.org/api/gevent.pywsgi.html>`_
    A WSGI server built on the `gevent <https://www.gevent.org/>`_
    coroutine-based networking library.

`Granian <https://github.com/emmett-framework/granian>`_
    A Rust HTTP server for Python applications. Supports ASGI/3, RSGI and WSGI interface applications.

`Gunicorn <https://gunicorn.org>`_
    WSGI HTTP Server for UNIX, fast clients and nothing else. This is
    a port of Unicorn_ to Python and WSGI.

`mod_wsgi <https://modwsgi.readthedocs.io>`_
    Python WSGI adapter module for Apache

`netius <https://netius.hive.pt/>`_
    Netius is a Python network library that can be used for the rapid 
    creation of asynchronous non-blocking servers and clients. It has no 
    dependencies, it's cross-platform, and brings some sample netius-powered 
    servers out of the box, namely a production-ready WSGI server.

`phusion passenger <https://www.phusionpassenger.com/>`_
    "proof of concept" WSGI since 2008 (1.x), support upgraded to
    "beta" in version 3 (with limitations e.g. requires Ruby even when
    unused) and first-class in Passenger 4.

`twisted.web <https://docs.twisted.org/en/stable/web/howto/web-in-60/wsgi.html>`_
   A WSGI server based on Twisted Web's HTTP server (requires Twisted
   8.2 or later).

`uWSGI <https://uwsgi-docs.readthedocs.io/en/latest/>`_
   Fast, self-healing, developer-friendly WSGI server, meant for
   professional deployment and development of Python Web applications.
   *Note: uWSGI is in maintenance mode: only bug and security fixes are
   expected, no new features.*

`Waitress <https://docs.pylonsproject.org/projects/waitress/en/latest/>`_
    A production-quality pure-Python WSGI server with very acceptable
    performance. It supports Windows, Python 3 and PyPy, and has no
    dependencies except the standard library.

`werkzeug.serving <https://werkzeug.palletsprojects.com/en/stable/serving/>`_
    Werkzeug's multithreaded and multiprocessed development
    server. Wraps wsgiref_ to add a reloader, multiprocessing, static
    files handling and SSL.

`wsgiref <https://docs.python.org/3/library/wsgiref.html>`_
    Included as part of the standard library; it includes a simple
    HTTP server, a CGI server (for running any WSGI application as a
    CGI script), and a framework for building other servers.

.. _Unicorn:
    https://yhbt.net/unicorn/
