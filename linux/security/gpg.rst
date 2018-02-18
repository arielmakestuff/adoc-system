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

Configuration files
-------------------

The default configuration files are ~/.gnupg/gpg.conf and ~/.gnupg/dirmngr.conf.

By default, the gnupg directory has its permissions set to 700 and the files
it contains have their permissions set to 600. Only the owner of the directory
has permission to read, write, and access the files. This is for security
purposes and should not be changed. In case this directory or any file inside
it does not follow this security measure, you will get warnings about unsafe
file and home directory permissions.

Append to these files any long options you want. Do not write the two dashes,
but simply the name of the option and required arguments. You will find
skeleton files in /usr/share/gnupg. These files are copied to ~/.gnupg the
first time gpg is run if they do not exist there.

=====
Setup
=====

Create master key pair
----------------------

.. code:: bash

   $ gpg --full-gen-key

The command will prompt for answers to several questions. For general use most
people will want:

* the RSA (sign only) and a RSA (encrypt only) key.

* a keysize of the default value (2048). A larger keysize of 4096 "gives us
  almost nothing, while costing us quite a lot"[1].

* an expiration date. A period of a year is good enough for the average user.
  This way even if access is lost to the keyring, it will allow others to know
  that it is no longer valid. Later, if necessary, the expiration date can be
  extended without having to re-issue a new key.

* your name and email address. You can add multiple identities to the same key
  later (e.g., if you have multiple email addresses you want to associate with
  this key).

* no optional comment. Since the semantics of the comment field are not
  well-defined, it has limited value for identification.

* a secure passphrase.
