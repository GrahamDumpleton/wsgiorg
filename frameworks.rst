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


`appier <https://appier.hive.pt>`_
  Appier is an object-oriented Python web framework built for super fast 
  app development. It's as lightweight as possible, but not too lightweight. 
  It gives you the power of bigger frameworks, without their complexity.
`Bottle <https://bottlepy.org>`_
    Bottle is a fast and simple micro-framework for small
    web-applications. It offers request dispatching (Routes) with url
    parameter support, Templates, key/value Databases, a build-in HTTP
    Server and adapters for many third party WSGI/HTTP-server and
    template engines. All in a single file and with no dependencies
    other than the Python Standard Library.
`CherryPy <https://cherrypy.dev>`_
    CherryPy is a pythonic, object-oriented web development framework.
    Includes support for WSGI servers.  It can live alongside other
    WSGI frameworks, applications, and middleware.
`Django <https://www.djangoproject.com/>`_
    Includes support for WSGI servers
`Falcon <https://falconframework.org/>`_
    Falcon is a high-performance Python framework for building cloud 
    APIs. It encourages the REST architectural style, and tries to do 
    as little as possible while remaining highly effective.
`Flask <https://flask.palletsprojects.com/>`_
    Flask is a microframework for Python based on Werkzeug, Jinja 2
    and good intentions.

    It inherits its high WSGI usage and compliance from Werkzeug.
`PoorWSGI <https://github.com/PoorHttp/PoorWSGI>`_
    Poor WSGI for Python is light WGI connector with uri routing between WSGI
    server and your application. It have mod_python compatible request object,
    which is post to all uri or http state handler.
`Pyramid <https://trypyramid.com/>`_
    Merger of the Pylons and repoze.bfg projects, Pyramid is a
    minimalist web framework aiming at composability and making
    developers paying only for what they use.
`TurboGears <https://turbogears.org/>`_
    Database-driven app in minutes; inherits its WSGI support from
    CherryPy.
`web.py <https://webpy.org/>`_
    Makes web apps.  A small RESTful library.
`web2py <https://web2py.com/>`_
    A full stack framework includes its own Database Abstraction Layer
    (with support for SQLite, MySQL, PostgreSQL, MSSQL, DB2, Informix,
    Oracle, FireBase, Ingres and Google App Engine), its own template
    language, and a web based IDE.  web2py itself is a WSGI app.  Not
    related to web.py.
`Zope <https://zope.readthedocs.io>`_
    The Zope web application server has been using WSGI as the default
    publishing method starting with version 4.


Deprecated Systems
------------------

These systems still exist but got replaced by others or are
unmaintained.

`Pylons <https://pylonshq.com/>`_
    Full-stack Python web development framework combining the very
    best from the worlds of Ruby, Python and Perl.

    Pylons has been superseded by pyramid_ .
