Throwing Errors
===============

:Title: x-wsgiorg.throw_errors
:Author: Ian Bicking <ianb@colorstudy.com>
:Discussions-To: Python Web-SIG <web-sig@python.org>
:Status: Proposed
:Created: 13 Nov 2006

.. contents::

Abstract
--------

WSGI applications are generally not supposed to raise exceptions,
instead handling their own errors (possibly returning a ``500 Server
Error`` response).  But in some context it is *desired* that
unexpected exceptions be allowed to bubble up.  This specification
defines a key to set in this circumstance.

Rationale
---------

When in a testing context it is undesirable for an application to
handle its own errors.  Typically the test framework is better at
handling the errors, either through error formatting or by dropping
into a debugger like `pdb
<http://python.org/doc/current/lib/module-pdb.html>`_.

Additionally when an exception catcher is installed in a stack,
ideally it will be used for all exceptions.  This allows for
centralized configuration (for example, when emails are sent when
errors occur).  Dynamically disabling any other exception catchers is
often ideal in this situation.

Specification
-------------

An exception catcher should check for
:envvar:`x-wsgiorg.throw_errors`.  If it is true, it should not try to
catch exceptions.  This need only be checked as the application is
being entered, it should not be checked later.  Applications should
not try to set this to effect middleware that *wraps* them, only to
effect applications they may call.

Example
--------

A simple exception catcher::

  class ExceptionCatch(object):
      def __init__(self, app):
          self.app = app
      def __call__(self, environ, start_response):
          if environ.get('x-wsgiorg.throw_errors'):
              return self.app(environ, start_response)
          try:
              return self.app(environ, start_response)
          except:
              import sys, traceback, StringIO
              exc_info = sys.exc_info()
              start_response('500 Server Error', [('content-type', 'text/plain')], 
                             exc_info=exc_info)
              out = StringIO.StringIO()
              traceback.print_exc(file=out)
              return [out.getvalue()]

Problems
--------

* In theory an application may know better how to format an error
  response than the middleware exception catcher.  Of course, an
  application can ignore :envvar:`x-wsgiorg.throw_errors` if it thinks it is
  best (or if it has been explicitly configured to do so).

Other Possibilities
-------------------

* You can just get the unwrapped application object and test it.

Open Issues
-----------

* None I know of

Implementations
---------------

`WebTest <http://pythonpaste.org/webtest/>`_ sets a key
(:envvar:`paste.throw_errors`) during debugging, which allows it to do
functional testing of applications that have the
:mod:`paste.exceptions` middleware applied to them (that middleware
looks for the key and disables itself per-request when it sees it).

Zope 2 has its own flag on the (non-WSGI) request to do this, showing
substantial history for this technique.  Zope 3 uses something like
:envvar:`wsgi.handleErrors` in the WSGI environ to the same effect (it
shouldn't be using ``wsgi.``, but it does).
