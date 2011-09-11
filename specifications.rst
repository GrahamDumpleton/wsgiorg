Specifications related to WSGI
==============================

This page holds specifications (proposed, accepted, and withdrawn) that build on WSGI.

About these specifications
--------------------------

These specifications are written up here and discussed on `WEB-SIG
<http://www.python.org/community/sigs/current/web-sig/>`_.  Once
accepted, these can all use the ``wsgiorg.`` prefix for their keys.
Until accepted, please use ``x-wsgiorg.`` -- this is primarily so that
people who implement the specification before it is accepted will not
leave out-of-spec implementations around (except the obvious ones due
to the ``x-``).

To be "accepted" the proposal should have certain qualities:

1. The spec won't change without good reason, so you can start
   implementing against it (once it is "approved").
2. It's useful in multiple contexts; if one implementation is all
   anyone will ever need, then just make your implementation.  Feel
   free to discuss it, but you don't need anyone's approval.
3. Some eyes have been on it, and it's been reviewed by multiple
   people.

There are certain advantages:

1. Having implemented either side, you can expect that maybe
   *someone* will care (either producing or consuming what you are
   looking for).
2. Someone won't implement something they think is the same, but
   isn't, because the document specifies the requirements
   sufficiently.
3. New proposals will take old proposals into account, and so they
   shouldn't overlap or repeat their purposes.

There's no particular process for a proposal to become accepted.
*Someone* else should like your proposal (+1, not just +0), and
probably no one should be opposed (no -1's).

Unless noted otherwise, everything here can be assumed to be public
domain (in keeping with the purpose of posting material here).

Accepted
--------

.. toctree::
   :maxdepth: 1

   specifications/routing_args

Proposed
--------

.. toctree::
   :maxdepth: 1

   specifications/fdevent
   specifications/developer_auth
   specifications/avoiding_serialization
   specifications/simple_authentication
   specifications/throw_errors

Withdrawn
---------

.. toctree::
   :maxdepth: 1

   specifications/unicode_handling
   specifications/handling_post_forms

Wanted
------

These don't exist yet, but they could.  Write one?

* A standard place to put HTTP proxy scheme and host information
  (e.g., when a server acts as an HTTP proxy the request looks like
  ``GET http://hostname.org/path ...``, and we don't have a place to
  keep ``http://hostname.org``).
* Ben Bangert suggested a simple session standard, focused solely on
  the session ID (persistence handled elsewhere).  This is fairly
  modest but still useful.  This was in an email:
  http://mail.python.org/pipermail/web-sig/2006-January/001858.html
* Maybe a full session interface built on the session ID standard.
  This is an API proposed earlier:
  http://svn.colorstudy.com/home/ianb/proposed_session_interface.py
* Often debugging tools open security holes (for example,
  ``paste.evalexception`` gives you a Python prompt on every
  exception).  Authentication isn't really the right way to handle it,
  because debugging might involve logging in as various users.  A
  specification could just define a key that indicates when these
  debugging tools should be allowed.  This might get set by
  configuration, IP address, a cookie, etc.
* Debugging mode is something that can be used in all sorts of places;
  to increase verbosity, annotate output pages, displaying errors in
  the browser, etc.  Having a single key for turning on debugging mode
  would allow its consumption in lots of places.  Not as strict as
  authenticating.
* Some systems prefer that unexpected exceptions bubble up, like test
  frameworks.  A key could define this case (modelled on
  :envvar:`paste.throw_errors`) and thus disable exception catchers.
* Logging is a tricky situation.  The :mod:`logging` module allows for
  statically setting up logging systems, then configuring them at
  startup.  This often isn't the best way to set up logging.  Putting
  a :class:`logging.Logger` instance right in the environment might be
  better.  This requires some design and usage before setting on one
  spec.
* Request object wrapping the environment.
* Thread-local values are a common technique in web frameworks,
  allowing global objects or functions to return request-specific
  information.  This pattern could be codified into one core system,
  using some feedback from existing systems (which have their
  advantages and flaws).
* Configuration takes fairly common forms, usually a dict of some
  sort.  It could be put somewhere standard.
* Maybe Paste Deploy's entry points could be standardized.  (Paste
  Deploy itself only consumes those entry points; other consumers are
  possible and packages implementing those entry points don't
  introduce any dependency on Paste Deploy)
* A way to extend :mod:`wsgiref.validate` to add more validation, for
  all these new specs.  (Probably this is an implementation, not a
  spec)
* A way to describe custom keys, maybe associated with the validation.
* Anchors for doing recursive calls, similar to
  :mod:`paste.recursive`.  (it's kind of an old module that is more
  complicated than it needs to be)
* A place to put a database transaction manager
* More user-based information than just ``REMOTE_USER``; like
  :envvar:`wsgiorg.user_info`?  The basics of this are described in
  :doc:`specifications/simple_authentication`, but it doesn't cover
  anything advanced.

These can be written based on
:doc:`specifications/specification_template`.
