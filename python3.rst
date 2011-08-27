Python 3
========

:pep:`3333` aims to resolve issues with Python 3 and WSGI.

This page is intended to collect ideas and proposals about WSGI amendments for Python 3.

See also :doc:`amendments-1.0`

Presentation, at DjangoCon 2010 (by Armin Ronacher)
---------------------------------------------------

 * Slides: `slideshare
   <http://www.slideshare.net/mitsuhiko/wsgi-on-python-3>`_ / `scribd
   <http://www.scribd.com/doc/31845512/WSGI-on-Python-3>`_
 * `Video on blip.tv <http://blip.tv/file/3677288>`_
 * `Blog post commentary by Armin
   <http://lucumr.pocoo.org/2010/5/25/wsgi-on-python-3>`_
 * `Reinout van Rees's reaction and commentary
   <http://reinout.vanrees.org/weblog/2010/05/24/future-django-wsgi.html>`_


Latest discussions
------------------

 * Main discussions occur on `the WEB-SIG mailing list
   <http://mail.python.org/mailman/listinfo/web-sig>`_
 * `'WSGI on Python 3' thread
   <http://www.mail-archive.com/web-sig@python.org/msg03346.html>`_
 * Graham Dupleton's 2009 `A roadmap for the Python WSGI specification
   <http://blog.dscpl.com.au/2009/09/roadmap-for-python-wsgi-specification.html>`_
   which describe all the proposals extensively (`author:` Graham

Proposals
---------

There's lots of discussions about the type of data (bytes versus
unicode) in various places of the specification.

The actual competitors are:
 * **mod_wsgi** implementation, formalized in a `draft WSGI 1.1 PEP
   <http://hg.xavamedia.nl/peps/file/tip/wsgi-1.1.txt>`_ (Dirkjan
   Ochtman, April 2010)
 * All **unicode** proposal, formalized in a `draft WSGI 2 PEP
   <http://bitbucket.org/ianb/wsgi-peps/src/tip/pep-XXXX.txt>`_ (Armin
   Ronacher, September 2009)
 * **web3** proposal, `an alternative approach
   <http://github.com/mcdonc/web3/blob/master/web3.rst>`_ (Chris
   !McDonough, July 2010)
 * **flat** proposal, optimized for ease of validation and low
   cognitive overhead (inputs are native except for the byte stream,
   all outputs are bytes)

Here is a summary table which outlines the bytes/unicode differences
between these proposals.

+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+
|                                             | WSGI 1.0 |     mod_wsgi      |      Unicode       |    web3     |       flat       |
+=============================================+==========+===================+====================+=============+==================+
| environ keys                                | bytes    |                                 native                                  |
+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+
| CGI values                                  | bytes    | native            | unicode            | bytes       | native (PEP 383) |
+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+
| :envvar:`SCRIPT_NAME`, :envvar:`PATH_INFO`, | bytes    | native            | unicode (utf-8)    | bytes       | native (PEP 383) |
| :envvar:`QUERY_STRING`                      |          |                   |                    |             |                  |
+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+
| :envvar:`wsgi.url_scheme`                   | bytes    | native            | unicode            | bytes       | native           |
+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+
| :envvar:`wsgi.input`                        |                                       bytes                                        |
+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+
| status line                                 | bytes    | bytes (or native) | unicode (or bytes) | bytes       | bytes            |
+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+
| headers                                     | bytes    | bytes (or native) | unicode or bytes   | bytes       | bytes            |
+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+
| response iterable                           | bytes    | bytes (or native) | bytes              | bytes       | bytes            |
+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+
| ``write()`` callback                        | bytes    | bytes (or native) | *(deprecated)*     | *(removed)* | *(removed)*      |
+---------------------------------------------+----------+-------------------+--------------------+-------------+------------------+

Notes:
 * a 'native string' is the primary string type for a particular
   Python implementation:

  * for Python 2.x this is a byte string,
  * for Python 3.x this is a Unicode string

 * unless otherwise stated, all Unicode strings are decoded using
   ``ISO-8859-1``
 * when :envvar:`SCRIPT_NAME` and :envvar:`PATH_INFO` are 'native' or
   'unicode', the environment should contain 2 additional values
   :envvar:`wsgi.script_name` and :envvar:`wsgi.path_info` which
   contain raw-bytes values.  (Except in the **flat** proposal, which
   assumes CGI variables are decoded as ``utf-8`` using :pep:`383`
   surrogateescape encoding, and that the raw bytes can thus be
   retrieved by re-encoding.)
 * details about the **mod_wsgi** proposal:

  * it is already implemented in mod_wsgi 3.0
  * almost entirely compatible with current **WSGI 1.0** for Python
    2
  * it runs the **WSGI 1.0** 'Hello World!' unchanged

 * details about the '''`Unicode`''' proposal:

  * the ``SCRIPT_NAME`` and ``PATH_INFO`` will be decoded as
    ``UTF-8``.  If it fails, they are decoded as ``ISO-8859-1``.  The
    name of the successful codec is stored in ``wsgi.uri_encoding``.
  * the ``REQUEST_URI`` variable is optional and stores the full URI as
    requested by the client.

 * details about the **web3** proposal:

  * this proposal does not try to be compatible with **WSGI 1.0**.  It
    targets Python 2.6+ and Python 3.1+.
  * all ``wsgi.*`` variables are intentionally renamed ``web3.*`` in the
    document.


== Draft implementations ==
 * `mod_wsgi 3.0+ <http://code.google.com/p/modwsgi>`_: see the page
   about `Python 3 support
   <http://code.google.com/p/modwsgi/wiki/SupportForPython3X>`_
 * `CherryPy 3.2
   <http://www.cherrypy.org/wiki/WhatsNewIn32#Python3Support>`_: see
   details about `CherryPy's WSGI 1.1 implementation
   <http://www.cherrypy.org/wiki/WSGI#WSGI1.0vsWSGI1.1>`_
 * `Experimental WSGI server for Python 3
   <http://bitbucket.org/mitsuhiko/wsgi3k/>`_
