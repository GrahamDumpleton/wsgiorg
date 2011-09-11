Techniques to avoid serializing the input or output when stacking middleware
============================================================================

:Title: Avoiding Serialization When Stacking Middleware
:Author: Ian Bicking <ianb@colorstudy.com>
:Discussions-To: Python Web-SIG <web-sig@python.org>
:Status: Proposed
:Created: 06-03-2007

.. contents::

Abstract
--------

This proposal gives a strategy for avoiding unnecessary serialization
and deserialization of request and response bodies.  It does so by
attaching attributes to :envvar:`wsgi.input` and the ``app_iter``, as
well as a new environment key
:envvar:`x-wsgiorg.want_parsed_response`.

Rationale
---------

Output-transforming middleware often has to parse the upstream
content, transform it, then serialize it back to a string for output.
The original output may have already been in the parsed form that the
middleware wanted.  Or there may be more middleware that does similar
transformations on the same kind of objects.

The same things apply to the parsing of :envvar:`wsgi.input`,
specifically parsing form data.  A similar strategy is presented to
avoid unnecessarily reparsing that data.

Specification
-------------

WSGI applications (or middleware) can return an app_iter that not only
serializes the output, but also has extra attributes.  An attribute is
given here, :attr:`app_iter.x_wsgiorg_parsed_response` which is a
function/method that takes one argument, the "type" of object that you
want to receive.  It may return that type of object, or None (meaning
it cannot produce that type of object).  Consumers should fall back on
normal parsing of the response if the method does not exist, or
returns None.

Similarly the :envvar:`wsgi.input` object may have the same method,
with the same meaning.

WSGI applications that want to lazily serialize their output have a
problem: they probably cannot calculate ``Content-Length`` without
doing the actual serialization.  Browsers typically want to know about
``Content-Length``, but WSGI middleware seldom cares, since it just
can get the content from app_iter regardless of its length.  WSGI
middleware that will transform the output can set
``environ['x-wsgiorg.want_parsed_response'] = True`` to give this hint
to the application.  Applications are thus encouraged to only lazily
serialize their output when that key is present and true.  (There is
no equivalent concept for :envvar:`wsgi.input`.)

The object returned by :meth:`x_wsgiorg_parsed_response` may be
modified in-place by the WSGI middleware using that object.  Producers
should make a copy if they do not want consumers modifying the object.

Example
--------

Two examples are provided: one for output, and one for input.

The output transformation parses the page with
:class:`lxml.etree.HTML` (from the `lxml
<http://codespeak.net/lxml/>`_ library) and replaces all ``<i>`` tags
with ``<em>`` tags.  First we show the middleware::

    import lxml.etree

    class EmTagMiddleware(object):
        def __init__(self, app):
            self.app = app
        def __call__(self, environ, start_response):
            parent_wants_parsed = environ.get('x-wsgiorg.want_parsed_response')
            environ['x-wsgiorg.want_parsed_response'] = True
            written_output = []
            captured_headers = []
            def repl_start_response(status, headers, exc_info=None):
                if exc_info:
                    raise exc_info[0], exc_info[1], exc_info[2]
                captured_headers[:] = [status, headers]
                return written_output.append
            app_iter = self.app(environ, repl_start_response)
            parsed = None
            if captured_headers and not written_output:
                method = getattr(app_iter, 'x_wsgiorg_parsed_response', None)
                if method:
                    parsed = method(lxml.etree._Element)
            if parsed is None:
                # Have to manually parse, because:
                #  a) start_response was called lazily
                #  b) the start_response writer was used
                #  c) app_iter.x_wsgiorg_parsed_response didn't exist
                #  d) that method returned None
                try:
                    for item in app_iter:
                        written_output.append(item)
                finally:
                    if hasattr(app_iter, 'close'):
                        app_iter.close()
                parsed = self.parse_body(''.join(written_output))
            status, headers = captured_headers
            new_body = self.transform_body(parsed)
            for i in range(len(headers)):
                if headers[i][0].lower() == 'content-length':
                    del headers[i]
                    break
            if parent_wants_parsed:
                new_app_iter = self.make_app_iter(new_body)
            else:
                serialized_body = serialize(new_body)
                headers.append(('Content-Length', str(len(serialized_body))))
                new_app_iter = [serialized_body]
            return new_app_iter

        def parse_body(self, body):
            return lxml.etree.HTML(body)

        def transform_body(self, root):
            for el in root.xpath('//i'):
                el.tag = 'em'
            return root

        def make_app_iter(self, body):
            return LazyLXML(body)

    def serialize(element):
        return lxml.etree.tostring(element)

    class LazyLXML(object):
        def __init__(self, body):
            self.body = body
            self.have_yielded = False
        def __iter__(self):
            return self
        def next(self):
            if self.have_yielded:
                raise StopIteration
            self.have_yielded = True
            return serialize(self.body)
        def x_wsgiorg_parsed_response(self, type):
            if type is lxml.etree._Element:
                return self.body
            return None

Here's a simpler example for parsing normal form inputs in
:envvar:`wsgi.input`::

    import cgi
    import urllib
    from cStringIO import StringIO

    def parse_form(environ):
        content_type = environ.get('CONTENT_TYPE', '')
        assert content_type in ['application/x-www-form-urlencoded', 'multipart/form-data']
        wsgi_input = environ['wsgi.input']
        method = getattr(wsgi_input, 'x_wsgiorg_parsed_response', None)
        if method:
            parsed = method(cgi.FieldStorage)
            if parsed is not None:
                return parsed
        form = cgi.FieldStorage(fp=wsgi_input, environ=environ, keep_blank_values=True)
        environ['wsgi.input'] = FakeFormInput(form)
        return form

    class FakeFormInput(object):
        def __init__(self, form):
            self.form = form
            self.serialized = None
        def x_wsgiorg_parsed_response(self, type):
            if type is cgi.FieldStorage:
                return self.form
            return None
        def read(self):
            if self.serialized is None:
                self._serialize()
            return self.serialized.read()
        def readline(self, *args):
            if self.serialized is None:
                self._serialize()
            return self.serialized.readline(*args)
        def readlines(self, *args):
            if self.serialized is None:
                self._serialize()
            return self.serialized.readlines(*args)
        def __iter__(self):
            if self.serialized is None:
                self._serialize()
            return iter(self.serialized)
        def _serialize(self):
            # XXX: Doesn't deal with file uploads, and multipart/form-data generally
            data = urllib.urlencode(self.form.list, True)
            self.serialized = StringIO(data)

Problems
--------

Obviously the code is not simple, but this is the nature of WSGI
output-transforming middleware.  Ideally a framework of some sort
would be used to construct this kind of middleware.

Something that replaces :envvar:`wsgi.input` (like the example) may
change the :envvar:`CONTENT_LENGTH` of the request; normalization
alone may change the length, even if the data is the same (e.g., there
are multiple ways to urlencode a string).  However, there's no way
without actually serializing to determine the proper length.  Ideally
requests like this should allow simply reading to the end of the
object, without needing a :envvar:`CONTENT_LENGTH` restriction (this
is not true for socket objects).  Ideally something like
``CONTENT_LENGTH="-1"`` would indicate this situation (simply a
missing :envvar:`CONTENT_LENGTH` generally means ``0``).  Another
option is to set it to 1 and simply return the entire serialized
response all at once.  :class:`cgi.FieldStorage` actually protects
against this.  Or set it to a very very large value, and allow reading
past the end (returning ``""``).  This is likely to work with most
consumers.  I'm not sure what effect -1 will have on different code.

Other Possibilities
-------------------

* You could simply parse everything ever time.
* You could pass data through callbacks in the environment (but this
  can break non-aware middleware).
* You can make custom methods and keys for each case.
* You can use something other than WSGI.

I think this specification offers advantages over all these options.

Open Issues
-----------

Should "type" be the class object?  A string describing the type?
Things like :class:`lxml.etree._Element` are a little unclean, since
the *actual* class isn't a public object (only the factory function
:func:`lxml.etree.Element`).  Also, there are occasionally times when
multiple classes implement the same interface.

The boolean :envvar:`x-wsgiorg.want_parsed_response` doesn't really
give any idea of what *kind* of object you want.  This is actually
something of a problem, because sometimes it's impossible to give that
kind of object.  For instance, if you want to transform images you
might want the PIL object for the image.  But if the response is HTML
there's no way to give this type.  Similarly if you are transforming
HTML then images don't mean anything to you, and you probably *do*
want them to come out as normal.  And potentially *both* a image
transformer and an HTML transformer are in the stack.  Should that key
actually hold a list of types that are of interest?

:meth:`x_wsgiorg_parsed_response` isn't a very good name for the
method on :envvar:`wsgi.input`, as it's not a response.
