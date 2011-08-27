Frameworks that run on WSGI
===========================

This is an alphabetic list of frameworks known to support WSGI.  The
level and nature of their support sometimes varies, as do the APIs
they provide.  The descriptions here focus on that, and not the flavor
of the frameworks themselves.  If you want to know more, follow the
links!

.. note:: Some frameworks really only support using pluggable WSGI
  servers, which means you get a number of options from HTTP, FastCGI,
  SCGI, threaded, forking, etc.  However, not all such frameworks live
  well alongside other frameworks in the same process, or may require
  extra configuration.  This is what is meant by noting when a
  framework supports WSGI servers, vs. a framework that supports a
  greater number of WSGI compositions, especially the kind of things
  noted in :doc:`libraries` Please feel free to expand on
  the list, the descriptions, or to make corrections.

`bobo <http://bobo.digicool.com>`_
  Bobo is a light-weight framework. Its goal is to be easy to use and
  remember.
`Bottle <http://bottle.paws.de/>`_
    Bottle is a fast and simple mirco-framework for small
    web-applications. It offers request dispatching (Routes) with url
    parameter support, Templates, key/value Databases, a build-in HTTP
    Server and adapters for many third party WSGI/HTTP-server and
    template engines. All in a single file and with no dependencies
    other than the Python Standard Library.
`CherryPy <http://www.cherrypy.org/>`_
    CherryPy is a pythonic, object-oriented web development framework.
    Includes support for WSGI servers.  CherryPy 3 includes better
    support for living alongside other WSGI frameworks, applications,
    and middleware.
`Django <http://www.djangoproject.com/>`_
    Includes support for WSGI servers
`notmm <https://bitbucket.org/erob/notmm/overview>`_
    The notmm toolkit is a fork of Django that doesn't get in your
    way. Features includes improved WSGI support (Paste), SQLAlchemy,
    and very few developers! ;-)
`Pyramid <https://www.pylonsproject.org/projects/pyramid/about>`_
    Merger of the Pylons and repoze.bfg projects, Pyramid is a
    minimalist web framework aiming at composability and making
    developers paying only for what they use.
`QWeb <https://github.com/antonylesuisse/qweb>`_
    Another WSGI framework (not sure what the distinguishing features
    are)
`repoze.zope2 <http://repoze.org>`_ 
    A module that implements an analogue of the Zope 2 ZPublisher,
    with some major simplifications and cleanups. Its core mission is
    to allow publishing existing Zope2 applications in a WSGI
    environment that externalizes some of the features of "classic"
    Zope2 into middleware.
`TurboGears <http://turbogears.org/>`_
    Database-driven app in minutes; inherits its WSGI support from
    CherryPy.
`web.py <http://webpy.org/>`_
    Makes web apps.  A small RESTful library.
`web2py <http://web2py.com/>`_
    A full stack framework includes its own Database Abstraction Layer
    (with support for SQLite, MySQL, PostgreSQL, MSSQL, DB2, Informix,
    Oracle, FireBase, Ingres and Google App Engine), its own template
    laguage, and a web based IDE.  web2py itself is a WSGI app.  Not
    related to web.py.
`weblayer <http://packages.python.org/weblayer>`_
    weblayer is a lightweight, componentised package for writing WSGI
    applications.
`Zope 3 <http://www.zope.org/>`_
    The venerable Python web framework, recreated anew in Zope 3, and
    now a WSGI application.  It *seems* to have some WSGI bits deep
    inside the publisher, but they aren't really documented at this
    time.

Deprecated Systems
------------------

These systems still exist but got replaced by others or are
unmaintained.

`Clever Harold <http://pypi.python.org/pypi/CleverHarold/0.1/>`_
    Clever Harold is an ambitious web framework. It has many features
    for rapid, reusable, and reliable web application
    construction. Clever Harold is a complete WSGI framework. To build
    an application, you pick and choose the servers and components
    that fit your needs.
`Colubrid <http://wsgiarea.pocoo.org/colubrid/>`_
    Colubrid is a WSGI publisher which simplifies python web
    developement.  Colubrid is not a framework :-) Although some
    people like the idea of having found a framework in colubrid. All
    colubrid does for you is parsing form data / url parameters /
    cookies and providing a url dispatcher. Colubrid was replaced by
    :ref:`werkzeug`.
`Nettri <http://code.google.com/p/nettri/>`_
    Nettri is a newcomer of Python World. It is under heavy
    development. Features includes CMS, Own template Engine, modules
    and more coming.
`Paste WebKit <http://pythonpaste.org/webkit/>`_
    An implementation of the `Webware <http://webwareforpython.org>`_
    servlet API using Paste infrastructure and WSGI.
`pycoon <http://code.google.com/p/pycoon/>`_
    Pythonic web development framework based on XML pipelines and WSGI
`Pylons <http://pylonshq.com/>`_
    Full-stack Python web development framework combining the very
    best from the worlds of Ruby, Python and Perl.

    Pylons has been superseded by pyramid_ .
`repoze.bfg <http://bfg.repoze.org>`_
    A Python WSGI-compliant web framework inspired by Zope, Pylons,
    and Django with built-in security and templating.

    repoze.bfg was renamed pyramid_ and moved under the Pylons
    project.
`RhubarbTart <http://pypi.python.org/pypi/RhubarbTart/0.5>`_
    A pure-WSGI dispatcher and simple framework, inspired by CherryPy.
`simpleweb <http://code.google.com/p/simpleweb-py/>`_
    A simple Python WSGI-compliant web framework inspired by Django,
    TurboGears, and web.py.
`skunk.web <http://code.google.com/p/satimol/>`_
    A totally WSGI-ified version of SkunkWeb.
`Wareweb <http://pythonpaste.org/wareweb/>`_
    A rethinking of the Webware/WebKit servlet model, in a pure-WSGI
    framework.  Not used widely.
`WebStack <http://www.boddie.org.uk/python/WebStack.html>`_
    WebStack is a package which provides a simple, common API for
    Python Web applications, allowing such applications to run within
    many different environments with virtually no changes to
    application code.
