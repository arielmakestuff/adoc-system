#####
GnuPG
#####

*************
Configuration
*************

Directory location
==================

``$GNUPGHOME`` is used by GnuPG to point to the directory where its
configuration files are stored. By default ``$GNUPGHOME`` is not set and your
``$HOME`` is used instead; thus, you will find a ~/.gnupg directory right
after installation.

To change the default location, either run gpg this way::

    $ gpg --homedir path/to/file

or set the ``GNUPGHOME`` environment variable.

Configuration files
===================

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

*****
Setup
*****

Create master key pair
======================

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

Secure master key
=================

Backup existing configuration
-----------------------------

Make backups of your existing GnuPG files ($HOME/.gnupg). Keep them safe. If
something goes wrong during the following steps, you may need this to return
to a known good place.

::

    $ umask 077; tar -cf $HOME/gnupg-backup.tar -C $HOME .gnupg

(note: umask 077 will result in restrictive permissions for the backup.)

Create a new subkey for signing
-------------------------------

* Find your key ID::

      $ gpg --list-keys yourname
      $ gpg --edit-key YOURMASTERKEYID

* At the gpg> prompt: addkey

* This asks for your passphrase, type it in.

* Choose the "RSA (sign only)" key type.

* It would be wise to choose 4096 (or 2048) bit key size.

* Choose an expiry date (you can rotate your subkeys more frequently than the
  master keys, or keep them for the life of the master key, with no expiry).

* GnuPG will (eventually) create a key, but you may have to wait for it to get
  enough entropy to do so.

* Save the key: save

You can repeat this, and create an "RSA (encrypt only)" subkey as well, if you
like or if you need to. As mentioned above, keep in mind that the default
option when initially creating a new keypair is to create an encryption
subkey, so you probably have one already.

Backup current configuration
----------------------------

Backup the current configuration and store it some place safe::

    $ umask 077; tar -cf $HOME/gnupg-private.tar -C $HOME .gnupg

Remove private master key
--------------------------

.. note::

   The following requires gnupg version 2.1+

Delete the file ``$HOME/.gnupg/private-keys-v1.d/KEYGRIP.key`` where KEYGRIP
is the "keygrip" of the master key which can be found by running::

    $ gpg --with-keygrip --list-key YOURMASTERKEYID

(The private part of each key pair has a keygrip, hence this command lists one
keygrip for the master key and one for each subkey.) Note however that if the
keyring has just been migrated to the new format, then the now obsolete
``$HOME/.gnupg/secring.gpg`` file might still contain the private master key:
thus be sure to delete that file too if it is not empty.

Verify that ``gpg -K`` shows a ``sec#`` instead of just sec for your private key. That
means the secret key is not really there. See the also the presence of a
dummy OpenPGP packet in the output of::

    $ gpg --export-secret-keys YOURMASTERKEYID | gpg --list-packets.

Change master key password
--------------------------

Change the passphrase protecting the subkeys::

    $ gpg --edit-key YOURMASTERKEYID passwd

This way if your everyday passphrase is compromised, the private master key
will remain safe from someone with access to the backup: the private key
material on the backup, including the private master key, are protected by the
old passphrase.

Using the master key
====================

When you need to use the master keys, use the ``.gnupg`` stored in the
``gnupg-distribute.tar`` backup, and set the GNUPGHOME environment variable::

    $ export GNUPGHOME=/path/to/.gnupg
    $ gpg -K

Alternatively::

    $ gpg --homedir /path/to/.gnupg -K

The latter command should now list your private key with ``sec`` and not ``sec#``.
