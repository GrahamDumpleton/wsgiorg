WSGI Unicode Handling
=====================

:Title: WSGI Unicode Handling
:Author: Armin Ronacher <armin.ronacher@active-4.com>
:Status: Rejected
:Created: 1-Nov-2006

.. contents::

Rejected
--------

This proposal is rejected mainly because of those reasons:

* It's easy enough for applications to do that on their own
* Many applications don't use unicode objects
* there should be an easier and more flexible way for that issue

----

   From Ian Bicking: 

   I'll add some commentary here, since I was the primary critic (of
   the limited audience before Armin withdrew this specification).
   Leaving this proposal here hopefully will be useful to later people
   considering this problem.

   Changing the response app_iter is pretty heavy, and isn't really an
   extension to WSGI, it's a change to the core specification.
   Current WSGI implementors really expect ``str`` responses.  When
   ``str`` goes away in Python 3000, they will have to expect
   ``bytes`` responses too, but that's a relatively straight-forward
   (though not trivial) change.  Dealing with backward compatibility
   is quite difficult.

   The use cases I personally see in this is avoiding the confusion
   and overhead of encoding and decoding responses when there are
   intermediaries which handle the response in its unicode form.  This
   is not uncommon -- for instance, XML processing happens on unicode
   data, and ideally all text responses should be handled as unicode.
   Deciding the encoding, and then doing the proper decoding, is not
   completely trivial (though not terribly hard).  It is hard enough
   that people will and have avoided it, potentially working with
   ``str`` data when that was not correct.  Similarly, it is important
   to send either properly-encoded data, or to change the encoding in
   the headers.  Since encoding information can show up in multiple
   places (unfortunately) this can also be error-prone.

   Despite these problems, sending unencoded data opens up a whole
   bunch of other problems, and realistically we get the union of all
   problems because we *definitely* cannot remove the sending of
   encoding text data.  So everyone has to deal with both cases now,
   instead of just one case.

   Anyway, that's my take on this.  -- Ian

Abstract
--------

This specification proposes a possible implementation of unicode
support in WSGI. Current all WSGI application have to output ``str``
objects instead.

Motivation
----------

Python ships two types of strings subclassing the abstract base class
``basestring``. ``str`` and ``unicode``. In Python 3 ``unicode`` will
replace ``str`` and a new class ``bytes`` will be introduced
(:pep:`3100#atomic-types`, :pep:`3137`). Also today many developers
use ``unicode`` objects because support a wider range of characters
and functions like ``len()`` still return the correct output, even
when using multibyte encodings like ``utf-8``.

But at the moment all WSGI applications have to yield ``str`` objects
which require that uses encoder their data to a special encoding by
hand. WSGI middlewares don't know about the charset the application is
using etc.

Specification
-------------

A possible solution would be a new key in the environ called
:envvar:`wsgi.charset`. The WSGI gateway would set this to ``None``
per default which means that yielding of ``unicode`` objects results
in an exception. But if the charset is correctly defined all returned
``unicode`` objects get encoded in the defined encoding by the WSGI
gateway.

Middlewares could use this value too convert incomming form data to
unicode automatically so that the application developer doesn't have
to take care about this issue.

Problem
-------

If this environment key is updated by the application middlewares
would still see ``None`` as charset because it's updated on first
iteration only. So an application developer would need to wrap the
whole application including middlewares afterwards again with a new
middleware that updates this key. Another possibility would be that
the WSGI gateway provides a configuration value for the charset.

If encoding the output of the wsgi application the gateway must also
get the :envvar:`wsgi.charset` key each time a unicode object is
found. Caching won't work because the application must be able to
change the charset before each iteration::

    def app(environ, start_response):
        start_response('200 OK', [('Content-Type', 'text/plain')])
        environ['wsgi.charset'] = 'utf-8'
        yield u'Hällo Wörld'
        environ['wsgi.charset'] = 'iso-8895-15'
        yield u'Hällo Wörld'

Implementation
--------------

Here a very simple CGI gateway that implements this functionality::

    import os
    import sys

    def run_with_cgi(app, charset=None):
        environ = dict(os.environ.items())
        environ['wsgi.charset'] = charset
        environ['wsgi.input'] = sys.stdin
        environ['wsgi.errors']  = sys.stderr
        environ['wsgi.version'] = (1,0)
        environ['wsgi.multithread'] = False
        environ['wsgi.multiprocess'] = True
        environ['wsgi.run_once'] = True

        if environ.get('HTTPS','off').lower() in ('on','1'):
            environ['wsgi.url_scheme'] = 'https'
        else:
            environ['wsgi.url_scheme'] = 'http'

        headers_set = []
        headers_sent = []

        def write(data):
            if not headers_set:
                 raise AssertionError('write() before start_response()')
            elif not headers_sent:
                 status, response_headers = headers_sent[:] = headers_set
                 sys.stdout.write('Status: %s\r\n' % status)
                 for header in response_headers:
                     sys.stdout.write('%s: %s\r\n' % header)
                 sys.stdout.write('\r\n')
            if isinstance(data, unicode):
                charset = environ['wsgi.charset']
                if charset is None:
                    raise AssertionError('application returned unicode without '
                                         'defined charset')
                data = data.encode(charset)
            sys.stdout.write(data)
            sys.stdout.flush()

        def start_response(status,response_headers,exc_info=None):
            if exc_info:
                try:
                    if headers_sent:
                        raise exc_info[0], exc_info[1], exc_info[2]
                finally:
                    exc_info = None
            elif headers_set:
                raise AssertionError('Headers already set!')
            headers_set[:] = [status,response_headers]
            return write

        result = app(environ, start_response)
        try:
            for data in result:
                if data:
                    write(data)
            if not headers_sent:
                write('')
        finally:
            if hasattr(result,'close'):
                result.close()
