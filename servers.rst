Servers which support WSGI
==========================

This is an alphabetic list of WSGI servers.  In some cases these are
WSGI-only systems, in other cases a package includes a server.

Please feel free to expand the list or descriptions.  Direct links to
documentation on how to use the server is especially appreciated.

`ajp-wsgi <http://www.saddi.com/software/ajp-wsgi/>`_
    A threaded/forking WSGI server implemented in C (it embeds a
    Python interpreter to run the actual application). It communicates
    with the web server via AJP, and is known to work with `mod_jk
    <http://tomcat.apache.org/connectors-doc/>`_ and `mod_proxy_ajp
    <http://httpd.apache.org/docs/2.2/mod/mod_proxy_ajp.html>`_. Also
    available in an SCGI flavor.

`Aspen <http://aspen.io>`_
    A pure-Python web server (using the CherryPy module mentioned
    next) with three hooks to hang your WSGI on.

`Bjoern <https://github.com/jonashaag/bjoern>`_
    A “screamingly fast Python WSGI server” and boasts that
    it is “the fastest, smallest and most lightweight WSGI server.”
    See performance testing `testing WSGI servers with 
    wrk <https://www.appdynamics.com/blog/engineering/a-performance-analysis-of-python-wsgi-servers-part-2/>`_
    See the `install instructions <https://github.com/jonashaag/bjoern/wiki/Installation>`_

`cherrypy.wsgiserver <http://docs.cherrypy.org/en/latest/advanced.html#wsgi-support>`_
    CherryPy's "high-speed, production ready, thread pooled, generic
    WSGI server." Includes SSL support.  Supports Transfer-Encoding:
    chunked. For details on running foreign (non-CherryPy) applications
    under the CherryPy WSGI server, see `WSGI Support
    <http://docs.cherrypy.org/en/latest/advanced.html#wsgi-support>`_.
    See also the
    `CherryPy wiki ModWSGI page <http://tools.cherrypy.org/wiki/ModWSGI>`_.

`chiral.web.httpd <http://chiral.j4cbo.com/trac>`_
    A fast HTTP server supporting WSGI, with extensions for
    Coroutine-based pages with deeply-integrated COMET support.

`cogen.web.wsgi <http://code.google.com/p/cogen/>`_
    WSGI server with extensions for coroutine oriented programming.

`FAPWS <http://www.fapws.org/>`_
    Fapws is a WSGI binding between Python and `libev
    <http://software.schmorp.de/pkg/libev.html>`_.

    See also: `author's block
    <http://william-os4y.livejournal.com/>`_, `GoogleGroup
    <http://groups.google.com/group/fapws>`_.

`fcgiapp <http://cheeseshop.python.org/pypi/fcgiapp/1.4>`_
    fcgiapp is a Python wrapper for the C FastCGI SDK. It's used by
    PEAK's FastCGI servers to provide WSGI-over-FastCGI.

`flup <http://www.saddi.com/software/flup/>`_
    Includes threaded and forking versions of servers that support
    FastCGI, SCGI, and AJP protocols.

`gevent-fastcgi <https://github.com/momyc/gevent-fastcgi>`_
    WSGI-over-FastCGI server implemented using `gevent <http://www.gevent.org/>`_ coroutine-based networking library.
    Supports FastCGI connection multiplexing. Includes adapters for Django and other
    frameworks that use PasteDeploy.

`Granian <https://github.com/emmett-framework/granian>`_
    A Rust HTTP server for Python applications. Supports ASGI/3, RSGI and WSGI interface applications.

`Gunicorn <http://gunicorn.org>`_
    WSGI HTTP Server for UNIX, fast clients and nothing else. This is
    a port of Unicorn_ to Python and WSGI.

`ISAPI-WSGI <http://code.google.com/p/isapi-wsgi/>`_
    An implementation of WSGI for running as a ISAPI extension under
    IIS.

`James <http://wsgiarea.pocoo.org/james/>`_
    James provides a very simple multi-threaded WSGI server
    implementation based on the HTTPServer from Python's standard
    library. (*unmaintained*)

`Julep <http://code.google.com/p/julep/>`_
    A WSGI Server inspired by Unicorn_, written in pure Python.

`m2twisted <http://www.python.org/pypi/m2twisted>`_
    WSGI server built with M2Crypto and twisted.web2 with some SSL
    related tricks. Used with client side smart cards and it is also
    possible to run the HTTPS server with a key in a HSM (like a
    crypto token)

`modjy <http://modjy.xhaus.com/>`_
    Modjy is a java servlets to WSGI gateway that enables the running
    of `jython <http://www.jython.org>`_ WSGI applications inside
    `java servlet containers
    <http://en.wikipedia.org/wiki/Java_Servlet>`_.

`mod_wsgi <http://www.modwsgi.org>`_
    Python WSGI adapter module for Apache

`NWSGI <http://nwsgi.codeplex.com/>`_
    NWSGI is a .NET implementation of the Python WSGI specification
    for IronPython and IIS. This makes it easy to run Python web
    applications on Windows Server. This is a potential alternative to
    ISAPI + ISAPI_WSGI modules.

`netius <http://netius.hive.pt/>`_
    Netius is a Python network library that can be used for the rapid 
    creation of asynchronous non-blocking servers and clients. It has no 
    dependencies, it's cross-platform, and brings some sample netius-powered 
    servers out of the box, namely a production-ready WSGI server.

`paste.httpserver <http://pythonpaste.org/modules/httpserver.html#module-paste.httpserver>`_
    Minimalistic threaded WSGI server built on BaseHTTPServer. Doesn't
    support Transfer-Encoding: chunked.

`phusion passenger <https://www.phusionpassenger.com/>`_
    "proof of concept" WSGI since 2008 (1.x), support upgraded to
    "beta" in version 3 (with limitations e.g. requires Ruby even when
    unused) and first-class in Passenger 4.

`python-fastcgi <http://cheeseshop.python.org/pypi/python-fastcgi/1.1>`_
    python-fastcgi is a lightweight wrapper around the Open Market
    FastCGI C Library/SDK. It includes threaded and forking WSGI
    server implementations.

`Spawning <http://pypi.python.org/pypi/Spawning>`_
   .. n/a

`twisted.web <http://twistedmatrix.com/trac/wiki/TwistedWeb/>`_
   A WSGI server based on Twisted Web's HTTP server (requires Twisted
   8.2 or later).

`unit <https://unit.nginx.org>`_
   A new lightweight declarative-configuration application server by
   NGINX, Inc, with `built-in first-class support for WSGI (and ASGI)
   <https://unit.nginx.org/configuration/#python>`_, including
   websocket support.

`uWSGI <http://projects.unbit.it/uwsgi>`_
   Fast, self-healing, developer-friendly WSGI server, meant for
   professional deployment and development of Python Web applications.

`werkzeug.serving <http://werkzeug.pocoo.org/docs/serving/>`_
    Werkzeug's multithreaded and multiprocessed development
    server. Wraps wsgiref_ to add a reloader, multiprocessing, static
    files handling and SSL.

`wsgid <http://wsgid.com>`_
    Wsgid is a generic WSGI handler for mongrel2_ webserver. Wsgid offers
    a complete daemon environment (start/stop/restart) to your app workers, 
    including automatically re-spawning of processes.
    
`WSGIserver <https://fgallaire.github.io/wsgiserver>`_
    WSGIserver is a high-speed, production ready, thread pooled, generic WSGI
    server with SSL support for both Python 2 (2.6 and above) and Python 3
    (3.1 and above). WSGIserver is a one file project with no dependency.

`WSGIUtils <http://www.owlfish.com/software/wsgiutils/index.html>`_
    Includes a threaded HTTP server.

`wsgiref <http://docs.python.org/library/wsgiref.html>`_ (`Python 3
<http://docs.python.org/py3k/library/wsgiref.html>`_)
    Included as part of thef standard library since Python 2.5; it
    includes a threaded HTTP server, a CGI server (for running any
    WSGI application as a CGI script), and a framework for building
    other servers.

    For versions prior to Python 2.5, see `wsgiref's original home
    <http://peak.telecommunity.com/wsgiref_docs/>`_.

.. _Unicorn:
    http://unicorn.bogomips.org/
.. _mongrel2:
    http://mongrel2.org
.. _Rack
    http://rack.github.com/
