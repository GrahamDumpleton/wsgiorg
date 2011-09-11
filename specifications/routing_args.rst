Where to put information parsed out of the request path
=======================================================

:Title: wsgiorg.routing_args
:Author: Ian Bicking <ianb@colorstudy.com>
:Discussions-To: Python Web-SIG <web-sig@python.org>
:Status: Accepted
:Created: 21-Oct-2006

.. contents::

Abstract
--------

This proposes a new standard environment key
``environ['wsgiorg.routing_args']`` to represent the results of more
complicated URL parsing strategies.

Rationale
---------

WSGI currently specifies the meaning of :envvar:`SCRIPT_NAME` and
:envvar:`PATH_INFO`, which allows generic prefix-based dispatchers to
be created.  These dispatchers can work with any WSGI application that
respects the meaning of these two variables.  The basic meaning of
:envvar:`SCRIPT_NAME` is *the portion of the path that has been
consumed* and :envvar:`PATH_INFO` is *the portion of the path left to
the application*.

Using these two variables more complex dispatchers cannot represent
the information they pull out of the request path.  This specification
simply defines a place where such dispatchers can put their
information: :envvar:`wsgiorg.routing_args`.

Specification
-------------

This specification defines a new key that can go in the WSGI
environment, :envvar:`wsgiorg.routing_args`.  This key is optional.

If a dispatcher (like `routes <http://routes.groovie.org/>`_ or
`selector <http://lukearno.com/projects/selector/>`_) pulls named
information out of the portion of the request path it parses, it can
put that information into ``environ['wsgiorg.routing_args']``.
``routing_args`` must be a two-tuple of ``(positional_args,
named_args)``, where ``positional_args`` is a sequence of arguments
that were captured positionally, and ``named_args`` is a dictionary of
the arguments that were given names.

Not all kinds of dispatchers will produce both positional and named
arguments -- some may only be capable of producing one or the other.
Similarly, not all consumers will know what to do with both positional
and named arguments.  Implementors putting together producers and
consumers of :envvar:`wsgiorg.routing_args` will have to choose
combinations that work for their combination of pieces.  Dispatchers
that do not produce one of these items must put in an empty tuple/list
or empty dictionary in for the missing item.

The values in :envvar:`wsgiorg.routing_args` need not be strings
(except for the keys of ``named_args``).  For instance, a dispatcher
is allowed to parse ``/archive/2005/10/01`` into ``((), {'date':
datetime.date(2005, 10, 1)})``.

Portions of the path that have been parsed should still be moved to
:envvar:`SCRIPT_NAME` (and removed from :envvar:`PATH_INFO`).

Types
-----

The objects in ``(positional_args, named_args)`` are intended to be
usable as ``func(*positional_args, **named_args)``.  Therefore
``positional_args`` must be coercable to a tuple, and ``named_args``
must be a dictionary with string keys (``str`` or unicode-ASCII).
Python does not allow dictionary-like but values for ``**named_args``
(except for actual ``dict`` objects).

Example
-------

This example is a dispatcher that is given regular expressions and
matching applications.  It checks each regular expression in turn, and
when one matches it moves the named groups into
:envvar:`wsgiorg.routing_args` and dispatches to the associated application.

::

    class RegexDispatch(object):

        def __init__(self, patterns):
            self.patterns = patterns

        def __call__(self, environ, start_response):
            script_name = environ.get('SCRIPT_NAME', '')
            path_info = environ.get('PATH_INFO', '')
            for regex, application in self.patterns:
                match = regex.match(path_info)
                if not match:
                    continue
                extra_path_info = path_info[match.end():]
                if extra_path_info and not extra_path_info.startswith('/'):
                    # Not a very good match
                    continue
                pos_args = match.groups()
                named_args = match.groupdict()
                cur_pos, cur_named = environ.get('wsgiorg.routing_args', ((), {}))
                new_pos = list(cur_pos) + list(pos_args)
                new_named = cur_named.copy()
                new_named.update(named_args)
                environ['wsgiorg.routing_args'] = (new_pos, new_named)
                environ['SCRIPT_NAME'] = script_name + path_info[:match.end()]
                environ['PATH_INFO'] = extra_path_info
                return application(environ, start_response)
            return self.not_found(environ, start_response)

        def not_found(self, environ, start_response):
            start_response('404 Not Found', [('Content-type', 'text/plain')])
            return ['Not found']

    dispatch_app = RegexDispatch([
        (re.compile(r'/archive/(?P<year>\d{4})/$'), archive_app),
        (re.compile(r'/archive/(?P<year>\d{4})/(?P<month>\d{2})/$'),
         archive_app),
        (re.compile(r'/archive/(?P<year>\d{4})/(?P<month>\d{2})/(?P<article_id>\d+)$'),
         view_article),
    ])
