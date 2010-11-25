Six: Python 2 and 3 Compatibility Library
=========================================

.. module:: six
   :synopsis: Python 2 and 3 compatibility

.. moduleauthor:: Benjamin Peterson <benjamin@python.org>
.. sectionauthor:: Benjamin Peterson <benjamin@python.org>


Six provides simple utilities for wrapping over differences between Python 2 and
Python 3.

Six can be downloaded on `PyPi <http://pypi.python.org/pypi/six/>`_.  Its bug
tracker and code hosting is on `BitBucket <http://bitbucket.org/gutworth/six>`_.

The name, "six", comes from the fact that 2*3 equals 6.  Why not addition?
Multiplication is more powerful, and, anyway, "five" has already been `snatched
away <http://codespeak.net/z3/five/>`_.


Indices and tables
------------------

* :ref:`genindex`
* :ref:`search`


Package contents
----------------

.. data:: PY3

   A boolean indicating if the code is running on Python 3.


Constants
>>>>>>>>>

Six provides constants that may differ between Python versions.  Ones ending
``_types`` are mostly useful as the second argument to ``isinstance`` or
``issubclass``.


.. data:: class_types

   Possible class types.  In Python 2, this encompasses old-style and new-style
   classes.  In Python 3, this is just new-styles.


.. data:: integer_types

   Possible integer types.  In Python 2, this is :func:`py2:long` and
   :func:`py2:int`, and in Python 3, just :func:`py3:int`.


.. data:: string_types

   Possible types for text data.  This is :func:`py2:basestring` in Python 2 and
   :func:`py3:str` in Python 3.


.. data:: text_type

   Type for representing textual data in Unicode.  This is :func:`py2:unicode`
   in Python 2 and :func:`py3:str` in Python 3.


.. data:: binary_type

   Type for representing binary data.  This is :func:`py2:str` in Python 2 and
   :func:`py3:bytes` in Python 3.


.. data:: MAXSIZE

   The maximum size of a container.


Here's example usage of the module::

   import six

   def dispatch_types(value):
       if isinstance(value, six.integer_types):
           handle_integer(value)
       elif isinstance(value, six.class_types):
           handle_class(value)
       elif isinstance(value, six.string_types):
           handle_string(value)


Object model compatibility
>>>>>>>>>>>>>>>>>>>>>>>>>>

Python 3 renamed the attributes of several intepreter data structures.  The
following accessors are available.  Note that the recommended way to inspect
functions and methods is the stdlib :mod:`py3:inspect` module.


.. function:: get_unbound_function(meth)

   Get the function out of unbound method *meth*.  In Python 3, unbound methods
   don't exist, so this function just returns *meth* unchanged.  Example
   usage::

      from six import get_unbound_function

      class X(object):
          def method(self):
              pass
      method_function = get_unbound_function(X.method)


.. function:: get_method_function(meth)

   Get the function out of method object *meth*.


.. function:: get_method_self(meth)

   Get the ``self`` of bound method *meth*.


.. function:: get_function_code(func)

   Get the code object associated with *func*.


.. function:: get_function_defaults(func)

   Get the defaults tuple associated with *func*.


.. function:: advance_iterator(it)

   Get the next item of iterator *it*.  :exc:`py3:StopIteration` is raised if
   the iterator is exhausted.  This is a replacement for calling ``it.next()``
   in Python 2 and ``next(it)`` in Python 3.


.. function:: callable(obj)

   Check if *obj* can be called.


Syntax compatibility
>>>>>>>>>>>>>>>>>>>>

These functions smooth over operations which have different syntaxes between
Python 2 and 3.


.. function:: exec_(code, globals=None, locals=None)

   Execute *code* in the scope of *globals* and *locals*.  *code* can be a
   string or a code object.  If *globals* or *locals* is not given, they will
   default to the scope of the caller.  If just *globals* is given, it will also
   be used as *locals*.


.. function:: print_(*args, *, file=sys.stdout, end="\n", sep=" ")

   Print *args* into *file*.  Each argument will be separated with *sep* and
   *end* will be written to the file at the last.

   .. note::

      In Python 2, this function imitates Python 3's :func:`py3:print` by not
      having softspace support.  If you don't know what that is, you're probably
      ok. :)


.. function:: reraise(exc_type, exc_value, exc_traceback=None)

   Reraise an exception, possibly with a different traceback.  In the simple
   case, ``reraise(*sys.exc_info())`` with an active exception (in an except
   block) reraises the current exception with the last traceback.  A different
   traceback can be specified with the *exc_traceback* parameter.


.. function:: with_metaclass(metaclass, base=object)

   Create a new class with base class *base* and metaclass *metaclass*.  This is
   designed to be used in class declarations like this: ::

      from six import with_metaclass

      class Meta(type):
          pass

      class Base(object):
          pass

      class MyClass(with_metaclass(Meta, Base)):
          pass


Binary and text data
>>>>>>>>>>>>>>>>>>>>

Python 3 enforces the distinction between far more rigoriously than does Python
2; binary data cannot be automatically coerced text data.  six provides the
several functions to assist in classifying string data in all Python versions.


.. function:: b(data)

   A "fake" bytes literal.  *data* should always be a normal string literal.  In
   Python 2, :func:`b` returns a 8-bit string.  In Python 3, *data* is encoded
   with the latin-1 encoding to bytes.


.. function:: u(text)

   A "fake" unicode literal.  *text* should always be a normal string literal.
   In Python 2, :func:`u` returns unicode, and in Python 3, a string.


.. data:: StringIO

   This is an fake file object for textual data.  It's an alias for
   :class:`py2:StringIO.StringIO` in Python 2 and :class:`py3:io.StringIO` in
   Python 3.


.. data:: BytesIO

   This is a fake file object for binary data.  In Python 2, it's an alias for
   :class:`py2:StringIO.StringIO`, but in Python 3, it's an alias for
   :class:`py3:io.BytesIO`.


Renamed modules and attributes compatibility
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

.. module:: six.moves
   :synopsis: Renamed modules and attributes compatibility

Python 3 reorganized the standard library and moved several functions to
different modules.  Six provides a consistent interface to them through the fake
:mod:`six.moves` module.  For example, to load the module for parsing HTML on
Python 2 or 3, write::

   from six.moves import html_parser

Similarly, to get the function to reload modules, which was moved from the
builtin module to the ``imp`` module, use::

   from six.moves import reload_module

For the most part, :mod:`six.moves` aliases are the names of the modules in
Python 3.  When the new Python 3 name is a package, the components of the name
are separated by underscores.  For example, ``html.parser`` becomes
``html_parser``.  In some cases where several modules have been combined, the
Python 2 name is retained.  This is so the appropiate modules can be found when
running on Python 2.  For example, ``BaseHTTPServer`` which is in
``http.server`` in Python 3 is aliased as ``BaseHTTPServer``.

Some modules which had two implementations have been merged in Python 3.  For
example, ``cPickle`` no longer exists in Python 3.  It's been merged with
``pickle``.  In these cases, fetching the fast version will load the fast one on
Python 2 and the merged module in Python 3.


.. note::

   The :mod:`py2:urllib`, :mod:`py2:urllib2`, and :mod:`py2:urlparse` modules
   have been combined in the :mod:`py3:urllib` package in Python 3.
   :mod:`six.moves` doesn't not support their renaming because their members
   have been mixed across several modules in that package.

Supported renames:

+------------------------------+-------------------------------------+---------------------------------+
| Name                         | Python 2 name                       | Python 3 name                   |
+==============================+=====================================+=================================+
| ``builtins``                 | :mod:`py2:__builtin__`              | :mod:`py3:builtins`             |
+------------------------------+-------------------------------------+---------------------------------+
| ``configparser``             | :mod:`py2:ConfigParser`             | :mod:`py3:configparser`         |
+------------------------------+-------------------------------------+---------------------------------+
| ``copyreg``                  | :mod:`py2:copy_reg`                 | :mod:`py3:copyreg`              |
+------------------------------+-------------------------------------+---------------------------------+
| ``cPickle``                  | :mod:`py2:cPickle`                  | :mod:`py3:pickle`               |
+------------------------------+-------------------------------------+---------------------------------+
| ``cStringIO``                | :func:`py2:cStringIO.StringIO`      | :class:`py3:io.StringIO`        |
+------------------------------+-------------------------------------+---------------------------------+
| ``http_cookiejar``           | :mod:`py2:cookielib`                | :mod:`py3:http.cookiejar`       |
+------------------------------+-------------------------------------+---------------------------------+
| ``http_cookies``             | :mod:`py2:Cookie`                   | :mod:`py3:http.cookies`         |
+------------------------------+-------------------------------------+---------------------------------+
| ``html_entities``            | :mod:`py2:htmlentitydefs`           | :mod:`py3:html.entities`        |
+------------------------------+-------------------------------------+---------------------------------+
| ``html_parser``              | :mod:`py2:HTMLParser`               | :mod:`py3:html.parser`          |
+------------------------------+-------------------------------------+---------------------------------+
| ``http_client``              | :mod:`py2:httplib`                  | :mod:`py3:http.client`          |
+------------------------------+-------------------------------------+---------------------------------+
| ``BaseHTTPServer``           | :mod:`py2:BaseHTTPServer`           | :mod:`py3:http.server`          |
+------------------------------+-------------------------------------+---------------------------------+
| ``CGIHTTPServer``            | :mod:`py2:CGIHTTPServer`            | :mod:`py3:http.server`          |
+------------------------------+-------------------------------------+---------------------------------+
| ``SimpleHTTPServer``         | :mod:`py2:SimpleHTTPServer`         | :mod:`py3:http.server`          |
+------------------------------+-------------------------------------+---------------------------------+
| ``queue``                    | :mod:`py2:Queue`                    | :mod:`py3:queue`                |
+------------------------------+-------------------------------------+---------------------------------+
| ``reduce``                   | :func:`py2:reduce`                  | :func:`py3:functools.reduce`    |
+------------------------------+-------------------------------------+---------------------------------+
| ``reload_module``            | :func:`py2:reload`                  | :func:`py3:imp.reload`          |
+------------------------------+-------------------------------------+---------------------------------+
| ``reprlib``                  | :mod:`py2:repr`                     | :mod:`py3:reprlib`              |
+------------------------------+-------------------------------------+---------------------------------+
| ``socketserver``             | :mod:`py2:SocketServer`             | :mod:`py3:socketserver`         |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter``                  | :mod:`py2:Tkinter`                  | :mod:`py3:tkinter`              |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_dialog``           | :mod:`py2:Dialog`                   | :mod:`py3:tkinter.dialog`       |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_filedialog``       | :mod:`py2:FileDialog`               | :mod:`py3:tkinter.FileDialog`   |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_scrolledtext``     | :mod:`py2:ScrolledText`             | :mod:`py3:tkinter.scolledtext`  |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_simpledialog``     | :mod:`py2:SimpleDialog`             | :mod:`py2:tkinter.simpledialog` |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_tix``              | :mod:`py2:Tix`                      | :mod:`py3:tkinter.tix`          |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_constants``        | :mod:`py2:Tkconstants`              | :mod:`py3:tkinter.constants`    |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_dnd``              | :mod:`py2:Tkdnd`                    | :mod:`py3:tkinter.dnd`          |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_colorchooser``     | :mod:`py2:tkColorChooser`           | :mod:`py3:tkinter.colorchooser` |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_commondialog``     | :mod:`py2:tkCommonDialog`           | :mod:`py3:tkinter.commondialog` |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_tkfiledialog``     | :mod:`py2:tkFileDialog`             | :mod:`py3:tkinter.filedialog`   |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_font``             | :mod:`py2:tkFont`                   | :mod:`py3:tkinter.font`         |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_messagebox``       | :mod:`py2:tkMessageBox`             | :mod:`py3:tkinter.messagebox`   |
+------------------------------+-------------------------------------+---------------------------------+
| ``tkinter_tksimpledialog``   | :mod:`py2:tkSimpleDialog`           | :mod:`py3:tkinter.simpledialog` |
+------------------------------+-------------------------------------+---------------------------------+
| ``urllib_robotparser``       | :mod:`py2:robotparser`              | :mod:`py3:urllib.robotparser`   |
+------------------------------+-------------------------------------+---------------------------------+
| ``winreg``                   | :mod:`py2:_winreg`                  | :mod:`py3:winreg`               |
+------------------------------+-------------------------------------+---------------------------------+
| ``xrange``                   | :func:`py2:xrange`                  | :func:`py3:range`               |
+------------------------------+-------------------------------------+---------------------------------+


Advanced - Customizing renames
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<

.. currentmodule:: six

It is possible to add additional names to the :mod:`six.moves` namespace.


.. function:: add_move(item)

   Add *item* to the :mod:`six.moves` mapping.  *item* should be a
   :class:`MovedAttribute` or :class:`MovedModule` instance.


.. function:: remove_move(name)

   Remove the :mod:`six.moves` mapping called *name*.  *name* should be a
   string.


Instances of the following classes can be passed to :func:`add_move`.  Neither
have any public members.


.. class:: MovedModule(name, old_mod, new_mod)

   Create a mapping for :mod:`six.moves` called *name* that references different
   modules in Python 2 and 3.  *old_mod* is the name of the Python 2 module.
   *new_mod* is the name of the Python 3 module.


.. class:: MovedAttribute(name, old_mod, new_mod, old_attr=None, new_attr=None)

   Create a mapping for :mod:`six.moves` called *name* that references different
   attributes in Python 2 and 3.  *old_mod* is the name of the Python 2 module.
   *new_mod* is the name of the Python 3 module.  If *new_attr* is not given, it
   defaults to *old_attr*.  If neither is given, they both default to *name*.
