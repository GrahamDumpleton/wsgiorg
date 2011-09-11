Simple Authentication
=====================

:Title: Simple Authentication
:Author: Ian Bicking <ianb@colorstudy.com>
:Discussions-To: Python Web-SIG <web-sig@python.org>
:Status: Proposed
:Created: 13-Nov-2006

.. contents::

Abstract
--------

This describes a simple pattern for implementing authentication in
WSGI middleware.  This does not propose any new features or
environment keys; it only describes a baseline recommended practice.

Rationale
---------

Authentication is probably the most common detail that should be
abstracted away from an application, as it is a concern most often
bound to a *deployment*.

Specification
-------------

There are two components to authentication:

 1. Indicating when a request is authenticated, and by who
 2. Responding that authentication is necessary

There are already two conventions for this:

 1. Put the username in ``REMOTE_USER``
 2. Respond with ``401 Unauthorized``

.. note:: Please do not confused ``401 Unauthorized`` with "permission
   denied".  Permission denied should be indicated with ``403
   Forbidden``.

``REMOTE_USER``:
    This should be the string username of the user, nothing more.
``401 Unauthorized``:
    Because middleware is handling the authentication, additional
    information is not required.  You do not (and should not) include
    a ``WWW-Authenticate`` header.  The middleware may include that
    header, or may change the response in some other way to handle the
    login.

Example
--------

The first example implements simple HTTP Basic authentication::

  class HTTPBasic(object):

      def __init__(self, app, user_database, realm='Website'):
          self.app = app
          self.user_database = user_database
          self.realm = realm

      def __call__(self, environ, start_response):
          def repl_start_response(status, headers, exc_info=None):
              if status.startswith('401'):
                  remove_header(headers, 'WWW-Authenticate')
                  headers.append(('WWW-Authenticate', 'Basic realm="%s"' % self.realm))
              return start_response(status, headers)
          auth = environ.get('HTTP_AUTHORIZATION')
          if auth:
              scheme, data = auth.split(None, 1)
              assert scheme.lower() == 'basic'
              username, password = data.decode('base64').split(':', 1)
              if self.user_database.get(username) != password:
                  return self.bad_auth(environ, start_response)
              environ['REMOTE_USER'] = username
              del environ['HTTP_AUTHORIZATION']
          return self.app(environ, repl_start_response)

      def bad_auth(self, environ, start_response):
          body = 'Please authenticate'
          headers = [
              ('content-type', 'text/plain'),
              ('content-length', str(len(body))),
              ('WWW-Authenticate', 'Basic realm="%s"' % self.realm)]
          start_response('401 Unauthorized', headers)
          return [body]

  def remove_header(headers, name):
      for header in headers:
          if header[0].lower() == name.lower():
              headers.remove(header)
              break

Problems
--------

* Strictly speaking, it is illegal to send a ``401 Unauthorized``
  response without the ``WWW-Authenticate header``.  If no middleware
  is installed, most browsers will treat it like a ``200 OK``.  There
  is also no way to detect if an appropriate middleware is installed.

* This doesn't give any other information about the user.  That
  information can go in other keys, but that is not addressed in this
  specification currently.

* Some login methods will redirect the user, and any POST request data
  will possibly be lost.  (Note that a specification like
  :doc:`handling_post_forms` helps address this problem.)

Other Possibilities
-------------------

* While you can add to this specification, I think it's the most
  logical and useful way to do authentication and better efforts can
  build on this base.

Open Issues
-----------

See Problems.
