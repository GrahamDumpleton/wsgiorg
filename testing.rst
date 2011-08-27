Testing tools for WSGI
======================

Any HTTP-based testing system can be used with WSGI applications.

Obviously any HTTP testing system can test any HTTP application.

However, some testing frameworks work more intimately with WSGI, and
provide the ability the call WSGI applications in a controlled
environment, with tracebacks and full use of debugging tools.

`WSGI Intercept
<http://darcs.idyll.org/~t/projects/wsgi_intercept/README.html>`_

    Intercepts normal Python calls to httplib, and redirects them to a
    WSGI application running in-process. Any testing tools written in
    Python can be made to test WSGI applications in-process.

`Paste <http://pythonpaste.org/>`_

    Includes a wrapper for WSGI applications to make testing WSGI
    applications convenient.  See `Testing Applications with Paste
    <http://pythonpaste.org/testing-applications.html>`_ for details.

`Twill <http://www.idyll.org/~t/www-tools/>`_

    See `Testing WSGI Apps with twill
    <http://ivory.idyll.org/articles/wsgi-intro/testing-wsgi-apps-with-twill.html>`_
    for a description of the specifics on plugging these together.
    WSGI Intercept was originally written for Twill.

`webtest <http://www.cherrypy.org/file/trunk/cherrypy/test/webtest.py>`_

    Extensions to unittest for web frameworks.

`webunit <http://mechanicalcat.net/tech/webunit/>`_

    Unit test your websites with code that acts like a web browser.

`zope.testbrowser <http://cheeseshop.python.org/pypi/ZopeTestbrowser>`_

    An easy to use programmatic web browser with special focus on
    testing. Used in Zope 3, but not Zope specific.

