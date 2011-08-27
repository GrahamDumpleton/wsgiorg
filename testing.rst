Testing tools for WSGI
======================

Any HTTP-based testing system can be used with WSGI applications.

Obviously any HTTP testing system can test any HTTP application.

However, some testing frameworks work more intimately with WSGI, and
provide the ability the call WSGI applications in a controlled
environment, with tracebacks and full use of debugging tools.

`WSGI Intercept <http://code.google.com/p/wsgi-intercept/>`_

    Intercepts normal Python calls to httplib, and redirects them to a
    WSGI application running in-process. Any testing tools written in
    Python can be made to test WSGI applications in-process.

`Twill <http://twill.idyll.org/>`_

    See `Testing WSGI Apps with twill
    <http://ivory.idyll.org/articles/wsgi-intro/testing-wsgi-apps-with-twill.html>`_
    for a description of the specifics on plugging these together.
    WSGI Intercept was originally written for Twill.

`WebTest <http://webtest.pythonpaste.org/en/latest/index.html>`_

    Extraction of ``paste.fixture.TestApp``, rewriting portions to use
    WebOb.

    Allows for testing WSGI applications without having to start a
    WSGI server.

`cherrypy.test.webtest
<http://www.cherrypy.org/file/trunk/cherrypy/test/webtest.py>`_

    Extensions to unittest for web frameworks.

`webunit <http://mechanicalcat.net/tech/webunit/>`_

    Unit test your websites with code that acts like a web browser.

`zope.testbrowser <http://cheeseshop.python.org/pypi/ZopeTestbrowser>`_

    An easy to use programmatic web browser with special focus on
    testing. Used in Zope 3, but not Zope specific.

