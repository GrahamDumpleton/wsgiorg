Middleware and libraries for WSGI
=================================

`Barrel <http://lukearno.com/projects/barrel/>`_
    Flexible WSGI authentication and authorization tools.

`Beaker <http://beaker.groovie.org/>`_
    Lighweight WSGI sessions middleware.

    Beaker’s starts with the Perl Cache::Cache module, which was
    ported for use in Myghty. Beaker was then extracted from this
    code, and has been substantially rewritten and modernized since.

`Deliverance <http://deliverance.openplans.org/>`_
    Deliverance is a tool to theme HTML, applying a consistent style
    to applications and static files regardless of how they are
    implemented, and separating site-wide styling from
    application-level templating.

`hatom2atom <http://lukearno.com/projects/hatom2atom>`_
    hatom2atom provides Python tools for use with
    hAtom2Atom.xsl. Includes a test runner that uses html/atom file
    pairs to test for expected output and a WSGI app that acts as a
    proxy to transform hAtom documents into Atom (that you are looking
    at now).

`lib537.httpy <http://www.zetadev.com/software/lib537/>`_
    Smooths over WSGI's worst warts. In addition to calling
    start_response and returning an iterable, httpy lets you return a
    string, or return or raise a Response object.

`Oort <http://oort.to/>`_
    A WSGI-enabled toolkit for creating RDF-driven web apps.

`Paste <http://pythonpaste.org/>`_
    Roughly a framework, though more of a set of tools for frameworks.
    Provides Integration layers with other frameworks like
    `CherryPaste <http://pythonpaste.org/cherrypaste/>`_, `DjangoPaste
    <http://pythonpaste.org/djangopaste/>`_ and `zope.paste
    <http://cheeseshop.python.org/pypi/zope.paste/0.1>`_.

`Paste Deploy <http://pythonpaste.org/deploy/>`_
    Configuration system for WSGI applications, servers, and
    middleware; both to configure individual components and to compose
    those components into a single running system.

`raptorizemw <http://pypi.python.org/pypi/raptorizemw/>`_
    A layer of WSGI middleware that adds a velociraptor to every page served.
    Fact:  every WSGI app is better with a raptor.

`Repoze <http://repoze.org>`_
    Repoze is an effort to bring Zope technologies to the larger
    Python web development community by breaking Zope up into pieces
    that fit into a WSGI deployment model.  This effort also allows
    existing Zope users to make use of WSGI technologies for
    development and deployment purposes, notably including the ability
    to run Zope 2 and Plone applications under WSGI servers.

`SchevoWsgi <http://cheeseshop.python.org/pypi/SchevoWsgi/>`_
    Provides integration between `Schevo
    <https://github.com/11craft/schevo>`_ and WSGI apps.

`selector <http://lukearno.com/projects/selector/>`_
    This distribution provides WSGI middleware for "RESTful" mapping
    of URL paths to WSGI applications. Selector now also comes with
    components for environ based dispatch and on-the-fly middleware
    composition.

`static <http://lukearno.com/projects/static/>`_
    This distribution provides an easy way to include static content
    in your WSGI applications. There is a convenience method for
    serving files located via pkg_resources. There are also facilities
    for serving mixed (static and dynamic) content using "magic" file
    handlers.  Python 2.4 string substitution and Kid template support
    are provided and it is easy to roll your own handlers. Note that
    this distribution does not require Python 2.4 or Kid unless you
    want to use those types of templates.

`ToscaWidgets <http://toscawidgets.org/>`_
    A web widget toolkit for Python to aid in the creation, packaging,
    and distribution of common view elements normally used in the
    Web. ToscaWidgets is an almost complete rewrite of TurboGears
    1.0's widgets in the spirit of TurboGears 2.0 philosophy of
    repackaging its services as independent WSGI components for
    easier maintenance and reuse in other Python web applications or
    frameworks.

`urlrelay <http://cheeseshop.python.org/pypi/urlrelay/>`_
    Simple RESTful URL dispatcher that passes HTTP requests to an WSGI
    application based on a matching a URL path regex pattern and,
    optionally, the HTTP request method.

.. _werkzeug-label:

`Werkzeug <http://werkzeug.pocoo.org/>`_
    Werkzeug started as a simple collection of various utilities for
    WSGI applications and has become one of the most advanced WSGI
    utility modules.  It includes a powerful debugger, full featured
    request and response objects, HTTP utilities to handle entity
    tags, cache control headers, HTTP dates, cookie handling, file
    uploads, a powerful URL routing system and a bunch of community
    contributed addon modules.

`WFront <http://discorporate.us/jek/projects/wfront/>`_
    Front-door dispatcher that directs HTTP requests based on "virtual
    host".  Includes tools to isolate WSGI apps from server deployment
    details.

`WHIFF WSGI HTTP Integrated File System Frames <http://whiff.sourceforge.net/>`_
    WHIFF reduces application complexity by providing an
    infrastructure for managing web application name spaces, a
    configuration template language for wiring named components into
    an application, and an applications programmer interface for
    accessing named components from Python and javascript modules.
    
`wsgiakismet <http://cheeseshop.python.org/pypi/wsgiakismet/>`_
    Validates form submissions against the Akismet service to verify
    that they are not comment spam.

`wsgiauth <http://cheeseshop.python.org/pypi/wsgiauth/>`_
    WSGI authentication middleware. Supports HTTP basic, digest, IP,
    HTML form, and OpenID-based authentication.

`WSGIFilter <http://pythonpaste.org/wsgifilter/>`_
    A simple framework for doing output-filtering of WSGI content.
    Works well with WSGIRemote.

`wsgiform <http://cheeseshop.python.org/pypi/wsgiform/>`_
    WSGI middleware for validating and parsing HTML form submissions.
    Supports automatic escaping of HTML and data sterilization.

`WSGI Intercept <http://darcs.idyll.org/~t/projects/wsgi_intercept/README.html>`_
    Redirects Python HTTP calls to an in-process WSGI application.
    This can allow HTTP API calls (e.g., REST, XML-RPC, etc) without
    actually touching the network.

`wsgilog <http://cheeseshop.python.org/pypi/wsgilog/>`_
    WSGI logging and event reporting middleware. Supports logging
    events in WSGI applications to STDOUT, time rotated log files,
    email, syslog, and web servers. Also supports catching and sending
    HTML-formatted exception tracebacks to a web browser for
    debugging.

`WSGIRemote <http://pythonpaste.org/wsgiremote/>`_
    Client library for doing RPC-style internal subrequests in a WSGI
    stack.  Also works for doing HTTP RPC requests.

`WSGIRewrite <http://www.python.org/pypi/WSGIRewrite/>`_
    Middleware for URL rewriting, uses the same syntax as Apache's
    mod_rewrite.

`wsgiserialize <http://cheeseshop.python.org/pypi/wsgiserialize/>`_
    Object serialization middleware for WSGI. Supported object
    serialization formats include: XML-RPC, JSON, YaML, marshal, and
    pickle.

`wsgistate <http://cheeseshop.python.org/pypi/wsgistate/>`_
    Session, HTTP cache control, and caching middleware for
    WSGI. Sessions are `flup
    <http://www.saddi.com/software/flup/>`_-compatible. Supports
    memory, filesystem, database, and memcached based backends.

`WSGIUtils <http://www.owlfish.com/software/wsgiutils/index.html>`_
    Includes a simple WSGI application (wsgiAdaptor) that provides
    basic authentication, signed cookies and persistent sessions.

`wsgiview <http://cheeseshop.python.org/pypi/wsgiview/>`_
    Turns any TurboGears/Buffet template plug-ins into WSGI
    middleware.

`wsgize <http://cheeseshop.python.org/pypi/wsgize/>`_
    WSGI without the WSGI. Provides middleware for WSGI-enabling
    Python callables including:

    * Middleware that makes non-WSGI Python functions, callable
      classes, or methods into WSGI applications
    * Middleware that automatically handles generating WSGI-compliant
      HTTP response codes, headers, and compliant iterators
    * An HTTP response generator
    * A secondary WSGI dispatcher

`yaro <http://lukearno.com/projects/yaro/>`_
    This distribution provides Yet Another Request Object (for WSGI)
    in a way that is intended to be simple and useful for web
    developers who don't want to have to know a lot about WSGI to get
    the job done. It's also a handy convenience for those who do like
    to get under the hood but would be happy to eliminate some
    boilerplate without the encumbrance of some
    all-singing-all-dancing framework.

deprecated
----------


`AuthKit <http://pypi.python.org/pypi/AuthKit>`_
    AuthKit is an authentication and authorization toolkit for WSGI
    applications and frameworks.

    The authentication middleware part is essentially an extension of
    paste.auth and there is an adaptor module providing support for
    `Pylons <http://pylonshq.com>`_ although it works with all WSGI
    apps.

`memento <http://lukearno.com/projects/memento/>`_
    This distribution provides code reloading middleware for use with
    your WSGI applications. Upon recieving each request, it forgets
    everything that it has imported since the last request so that it
    is imported all over again. The concept was inspired by the
    RollBackImporter used by Steve Purcell in `PyUnit
    <http://pyunit.sourceforge.net/notes/reloading.html>`_

`webstring <http://psilib.sourceforge.net/webstring.html>`_
    webstring is a template engine for programmers whose favorite
    template language is Python. webstring can be used to generate any
    text format from a template with the additional advantage of
    advanced XML and HTML templating using the lxml and cElementTree
    libraries.

`WSGIOverlay <http://pythonpaste.org/wsgioverlay/>`_
    Application-neutral macro templating language. Seems to be
    superseded by Deliverance.

`wsgixml <http://pypi.python.org/pypi/wsgixml/>`_
    WSGI middleware modules for XML processing

