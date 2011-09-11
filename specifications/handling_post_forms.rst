A specification for how to process POST form requests
=====================================================

:Title: Handling POST forms in WSGI
:Author: Ian Bicking <ianb@colorstudy.com>
:Discussions-To: Python Web-SIG <web-sig@python.org>
:Status: Withdrawn
:Created: 21-Oct-2006

.. contents::

Abstract
--------

This suggests a way that WSGI middleware, applications, and frameworks
can access POST form bodies so that there is less contention for the
:envvar:`wsgi.input` stream.

Reason for Withdrawl
--------------------

I decided that there were opportunities to decorate the
:envvar:`wsgi.input` stream itself, and have been pursing them in
`WSGIRemote <http://pythonpaste.org/wsgiremote/>`_.  I may describe
that strategy in a specification later.

Rationale
---------

Currently ``environ['wsgi.input']`` points to a stream that represents
the body of the HTTP request.  Once this stream has been read, it
cannot necessarily be read again.  It may not have a ``seek`` method
(none is required by the WSGI specification, and frequently none is
provided by WSGI servers).

As a result any piece of a system that looks at the request body
essentially takes ownership of that body, and no one else is able to
access it.  This is particularly problematic for POST form requests,
as many framework pieces expect to have access to this.  One notable
case is when a request "enters" a traditional web framework which
parses the POST form, then "exits" back to WSGI through some
framework-specific WSGI gateway.

The specification covers *library code* that multiple frameworks can
implement.  This is not functionality that is intended to be added to
a WSGI "stack".

Specification
-------------

This applies when certain requirements of the WSGI environment are
met::

    def is_post_request(environ):
        if environ['REQUEST_METHOD'].upper() != 'POST':
            return False
        content_type = environ.get('CONTENT_TYPE', 'application/x-www-form-urlencoded')
        return (content_type.startswith('application/x-www-form-urlencoded'
                or content_type.startswith('multipart/form-data'))

That is, it must be a POST request, and it must be a form request
(generally ``application/x-www-form-urlencoded`` or when there are
file uploads ``multipart/form-data``).

When this happens, the form can be parsed by
:class:`cgi.FieldStorage`.  The results of this parsing is put in
:envvar:`wsgi.post_form` as ``(new_wsgi_input, old_wsgi_input,
FieldStorage_object)``.

The ``new_wsgi_input`` can be used to check if an intermediary has
replaced the input since :envvar:`wsgi.post_form` was calculated.  If
the input has been changed, the :envvar:`wsgi.post_form` data should
be discarded.  The ``old_wsgi_input`` can be used if you want to get
access to the original input stream (which may be seekable, and so
still useful).

The replacement :envvar:`wsgi.input` guards against routines that
access the data but don't conform to this specification.  Ideally the
replacement will act like the original :envvar:`wsgi.input` (producing
the same data), but if not it should raise an exception.  The input
should not block or produce inaccurate data.

::

    def get_post_form(environ):
        assert is_post_request(environ)
        input = environ['wsgi.input']
        post_form = environ.get('wsgi.post_form')
        if (post_form is not None
            and post_form[0] is input):
            return post_form[2]
        # This must be done to avoid a bug in cgi.FieldStorage
        environ.setdefault('QUERY_STRING', '')
        fs = cgi.FieldStorage(fp=input,
                              environ=environ,
                              keep_blank_values=1)
        new_input = InputProcessed('')
        post_form = (new_input, input, fs)
        environ['wsgi.post_form'] = post_form
        environ['wsgi.input'] = new_input
        return fs

    class InputProcessed(object):
        def read(self, *args):
            raise EOFError('The wsgi.input stream has already been consumed')
        readline = readlines = __iter__ = read

By using this routing multiple consumers can parse a POST form,
accessing the form data in any order (later consumers will get the
already-parsed data).

Query String data
-----------------

Note that nothing in this specification touches or applies to the
query string (in ``environ['QUERY_STRING']``).  This is not parsed as
part of the process, and nothing in this specification applies to GET
requests, or to the query string which may be present in a POST
request.

Middleware
----------

While this proposal makes it more feasible for middleware to access
POST form data, it should not be read as encouraging middleware to do
so.  In particular, no consumer should ever *expect* that
:envvar:`wsgi.post_form` is in the request environment.  Also, no
intermediary should parse the POST form data unless it actually is
interested in that data -- access should be deferred until there is a
real need for the POST data.

Problems
--------

* This specification only works for parsing with
  :class:`cgi.FieldStorage`.  This is not the only parser possible,
  though it is the only parser in common usage.

* The API for :class:`cgi.FieldStorage` is not particularly well
  defined, so creating compatible parsers is difficult.

* :class:`cgi.FieldStorage` doesn't have any unicode handling (it has
  to be done higher up).

* Ideally middleware should just not access "envvar:`wsgi.input`;
  people can (and have) read this specification as encouraging
  middleware to do this parsing.

* In an ideal world :envvar:`wsgi.input` would stick around, either as
  a temporary file or as a file that was a lazy serialization of the
  parsed data.

Other Possibilities
-------------------

* One of the simplest possibilities is to add this information to
  ``environ['wsgi.input']`` itself as a separate attribute.  E.g.::

    fs = getattr(environ['wsgi.input'], 'cgi_FieldStorage', None)
    if fs is None: # parse and replace wsgi.input...

  There's a certain elegance to keeping :envvar:`wsgi.input`
  self-describing and movable.

Open Issues
-----------

1. This doesn't address non-form-submission ``POST`` requests.  Most
   of the same issues apply to such requests, except that frameworks
   tend not to touch the request body in that case.  The body may be
   large, so the actual contents of the request body shouldn't go in
   the environment.  Perhaps they could go in a temporary file, but
   this too might be an unnecessary indirection in many cases.  Also
   other kinds of request (like ``PUT``) that have a request body are
   not covered, for largely the same reason.  In both these cases, it
   is much easier to construct a new :envvar:`wsgi.input` that
   accesses whatever your internal representation of the request body
   is.

2. Is the tuple of information necessary in :envvar:`wsgi.post_form`,
    or could it just be the :class:`~cgi.FieldStorage` instance?
    Should all the information go in :envvar:`wsgi.input` directly?

3. Should :envvar:`wsgi.input` be replaced by ``InputProcessed``, or
   just left as is?  Or should we look for code that serializes
   :class:`~cgi.FieldStorage` objects back to parseable strings?

4. Does ``QUERY_STRING`` actually have to be set for ``cgi`` not to
   mess up, or is that just an issue with GET requests?
