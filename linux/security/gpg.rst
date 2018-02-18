#####
GnuPG
#####

=============
Configuration
=============

Directory location
------------------

``$GNUPGHOME`` is used by GnuPG to point to the directory where its
configuration files are stored. By default ``$GNUPGHOME`` is not set and your
``$HOME`` is used instead; thus, you will find a ~/.gnupg directory right
after installation.

To change the default location, either run gpg this way::

    $ gpg --homedir path/to/file

or set the ``GNUPGHOME`` environment variable.

=====
Setup
=====
