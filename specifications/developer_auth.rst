Developer Auth
==============

:Title: Developer Auth
:Author: Ian Bicking <ianb@colorstudy.com>
:Discussions-To: Python Web-SIG <web-sig@python.org>
:Status: Proposed
:Created: 31-Mar-2008

.. contents::

Abstract
--------

Many tools can be written for a WSGI stack which should only
accessible to developers.  For example, an interactive debugger in
response to sessions.  Or a template system might display the
underlying filenames that created a page.  Or profiling data.  In some
cases there are security implications to exposing this data, in other
cases it is harmless but undesirable to show this information to
normal users.  This specification offers a single, simple way to
detect if a user should be presented with this information.

Rationale
---------

So far these tools have been controlled by configuration, e.g.,
``debug = True``, or :option:`--debug` on the command line.  This
works but can be dangerous, as a deployer or developer can forget to
turn off tools.  Or, if it is controlled through Python code, it can
be difficult to enable on a site that wasn't intended to have the tool
on, e.g., if you want to debug a live site because you can't reproduce
a problem in development.  Also, configuration doesn't allow some
people to see these development tools while hiding them from other
people.  A per-request and secure authentication method is more
desirable.

This could be implemented using application-specific authentication
methods and permission levels.  This is undesirable because often
debugging is orthogonal to users -- you may want to debug a problem
only present when a low-permission or anonymous user is visiting the
site.  Also it is difficult to keep application and debugging
permissions coherent, which is probably why this technique is not used
by any tools.

Specification
-------------

Debugging tools should look for a key
:envvar:`x-wsgiorg.developer_user`.  This will contain some kind of
user name.  If it is empty or not present, then debugging tools should
not activate themselves, or should not expose any information in the
browser.

The user name can be used in logging, but all users are considered to
have the same permission level (total access).  The username must be a
``str``, but its contents are not constrained (an IP address, for
example, would be acceptable, or a name and email, with an embedded
space).

If a URL is protected except for developers, applications should
simply return ``403 Forbidden``.  Seamless login is not part of this
specification or its goals.  Some systems may be IP-controlled, for
example, and no login is possible.


Example
--------

This is a simple exception catcher that uses the key::

    import sys, traceback

    class CatchExceptions(object):
        def __init__(self, app):
            self.app = app
        def __call__(self, environ, start_response):
            if not environ.get('x-wsgiorg.developer_user'):
                return self.app(environ, start_response)
            try:
                return self.app(environ, start_response)
            except:
                start_response('500 Server Error', [('content-type', 'text/plain')],
                               sys.exc_info())
                return [traceback.format_exc()]

Here is a IP-restricted middleware that sets the key::

    class IPDeveloper(object):
        def __init__(self, app, ips=('127.0.0.1',)):
            self.app = app
            self.ips = ips
        def __call__(self, environ, start_response):
            if environ.get('REMOTE_ADDR') in self.ips:
                environ['x-wsgiorg.developer_user'] = environ['REMOTE_ADDR']
            return self.app(environ, start_response)

Problems
--------

* With security by obscurity in mind, it might be best if login
  methods weren't clear.  With ease of use in mind, easy logins are
  best.
* There's no levels of access.  Everyone is assumed to have complete
  access.  (You could add another custom key if you want to share
  extra information between the authentication and application layer.)
* This encourages people to do production deployments with debugging
  tools enabled.

Other Possibilities
-------------------

* Configuration
* Conditional middleware composition
* Application login systems
* Some other generalized authentication system (AuthKit, etc).

Open Issues
-----------

* Should ``401 Authorization Required`` be returned?  Potentially with
  ``WWW-Authenticate: x-wsgiorg.developer_user``.  This would signal
  to the middleware that a login should occur, which it may or may not
  ignore (it could translate that to ``403 Forbidden``).  This would
  make, for example, HTTP Basic authentication doable (since that
  authentication is per-request, and so you can't detect if a user
  already has logged in).  But HTTP Basic would probably be
  inappropriate for many systems, where a page is *filtered* by
  authentication, it isn't blocked.

Implementations
---------------

`DevAuth <http://devauth.openplans.org/>`_ implements the
authentication portion of this system.  `Deliverance
<http://deliverance.openplans.org>`_ and `Cabochon
<http://www.coactivate.org/projects/cabochon/project-home>`_ both use
DevAuth for access to backend logging and controls.

DevAuth implements a login form (which uses a cookie) and IP
restrictions.  This allows developers from selected IP addresses to
login.  No links are provided to the login form, instead developers
must know the location, or it should be documented in applications
using DevAuth.  Similarly there's no way for applications to reject a
request and suggest a login; when a user accesses something they are
not allowed to access the applications simply generate 403 Forbidden.
This is unlike user-oriented login forms which helpful; this is
distinctly unhelpful.
