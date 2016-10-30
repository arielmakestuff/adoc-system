==
ls
==

File and directory names
========================

By default, file and directory names that contain spaces are displayed
surrounded by single quotes. To change this behavior use the ``-N`` or
``--quoting-style=literal`` options. Alternatively, set the ``QUOTING_STYLE``
environment variable to literal. [#]_

.. [#] `<https://unix.stackexchange.com/questions/258679/
       why-is-ls-suddenly-wrapping-items-with-spaces-in-single-quotes>`_


Disable default quoting style globally
--------------------------------------

On systems with ``pam_env``, the ``QUOTING_STYLE`` environment variable can be
set globally by adding it to ``/etc/environment``. Example file contents::

    # This file is parsed by pam_env module
    #
    # Syntax: simple "KEY=VAL" pairs on separate lines
    #
    QUOTING_STYLE=literal


Disable default quoting style per-user
--------------------------------------

Each user can override the default quoting style in one of 2 ways:

1. Setting an alias for ``ls`` to map to ``ls -N``

2. Manually calling ``ls`` with the appropriate argument::

       $ ls -N

   Or::

       $ ls --quoting-style=literal
