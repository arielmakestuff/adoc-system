Changes
=======

Locale
-------

* setup system locale by running the following:

  .. code-block:: bash

     # localectl set-locale LANG=en_CA.UTF-8

Networking
-----------

systemd-networkd
~~~~~~~~~~~~~~~~

* `/etc/systemd/network/50-wired.network`:

  .. code-block:: ini

     [Match]
     Name=enp11s0

     [Network]
     Address=192.168.1.6/24
     Gateway=192.168.1.1
     DNS=192.168.1.1

* Start and enable the following services:

  1. `systemd-networkd.service`

  2. `systemd-resolved.service`

* Rename `/etc/resolv.conf` to `/etc/resolv.conf.orig`, and then run the
  following:

  .. code-block:: bash

     # ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

etckeeper
---------

Install `etckeeper` and initialize the `/etc` directory:

.. code-block:: bash

   # pacman -S etckeeper
   # cd /etc
   # etckeeper init

Rank mirrors
------------

Modify `/etc/pacman.d/mirrorlist` to leave uncommented only those mirrors you
want, and then run the following:

.. code-block:: bash

   # cd /etc/pacman.d
   # mv mirrorlist mirrorlist.unranked
   # rankmirrors -n 6 mirrorlist.unranked > mirrorlist

Additional users
----------------

Run the following command to add normal user and the admin user:

.. code-block:: bash

   # for u in smokybobo admin ; do useradd -m -s /bin/bash $u ; done

The admin user account will be used for `journalctl`, `systemctl`, `mount`,
`kill`, and `iptables`.

sudo
----

Require a user to be in the `wheel` group (but don't add anyone to it). To do
so, edit `/etc/pam.d/su` and `/etc/pam.d/su-1` and uncomment the following line::

    auth            required        pam_wheel.so use_uid

Assuming `openssh` is already installed, create a new ssh group, add a user
to it, and limit ssh logins to only users in the ssh group:

.. code-block:: bash

   # groupadd -r ssh
   # gpasswd -a user ssh
   # echo 'AllowGroups ssh' >> /etc/ssh/sshd_config

Add user to other needed groups:

.. code-block:: bash

   # for g in power network ; do gpasswd -a smokybobo $g ; done
   # for g in power network storage ; do gpasswd -a admin $g ; done

Create a new file in `/etc/sudoers.d`:

.. code-block:: bash

   % su
   Password:
   # visudo -f /etc/sudoers.d/local

In the `/etc/sudoers.d/local` file,

ssh
---

To disallow root ssh login, add the following to `/etc/ssh/sshd_config`::

    # Disable root login
    PermitRootLogin no

    # Disable password authentication
    PasswordAuthentication no

    # Enable public key authentication
    PubkeyAuthentication yes


If ssh should always be listening, then run the following service:

.. code-block:: bash

   # systemctl start sshd.service
   # systemctl enable sshd.service

If systemd should listen instead, and only spawn ssh when an incoming
connection is detected, then run the following service:

.. code-block:: bash

   # systemctl start sshd.socket
   # systemctl enable sshd.socket


Home directory
--------------

Special directories
~~~~~~~~~~~~~~~~~~~

Create the following directories:

1. `~/.local/share/python-venv`

2. `~/.local/stow`

3. `~/.local/bin`

4. `~/.config`

5. `~/.cache`

6. `~/.local/share`

7. `~/.local/stow`

8. `~/.local/src`

9. `~/src`

10. `~/.cache/archbuild`

11. `~/.cache/tmp`

12. `~/.ssh`

13. `~/.config/systemd/user`


ssh
~~~

Generate keypair using Ed25519:

.. code-block:: bash

   $ ssh-keygen -t ed25519
   Generating public/private ed25519 key pair.
   Enter file in which to save the key (/home/<username>/.ssh/id_ed25519):
   Enter passphrase (empty for no passphrase):
   Enter same passphrase again:
   Your identification has been saved in /home/<username>/.ssh/id_ed25519.
   Your public key has been saved in /home/<username>/.ssh/id_ed25519.pub.
   The key fingerprint is:
   SHA256:gGJtSsV8BM+7w018d39Ji57F8iO6c0N2GZq3/RY2NhI username@hostname
   The key's randomart image is:
   +--[ED25519 256]--+
   |   ooo.          |
   |   oo+.          |
   |  + +.+          |
   | o +   +     E . |
   |  .   . S . . =.o|
   |     . + . . B+@o|
   |      + .   oo*=O|
   |       .   ..+=o+|
   |           o=ooo+|
   +----[SHA256]-----+

Configure client options in `~/.ssh/config`::

    # Global options
    User <username>
    AddKeysToAgent yes

    # Home network
    Host 192.168.0.?
        IdentitiesOnly yes
        IdentityFile ~/.ssh/id_ed25519_home

    Host github.com
        IdentitiesOnly yes
        IdentityFile ~/.ssh/id_ed25519_github

ssh-agent
~~~~~~~~~

To have `ssh-agent` run via systemd user facilities, create a new file
`~/.config/systemd/user/ssh-agent.service` with the following content::

    [Unit]
    Description=SSH key agent

    [Service]
    Type=forking
    Environment=SSH_AUTH_SOCK=%t/ssh-agent.socket
    ExecStart=/usr/bin/ssh-agent -a $SSH_AUTH_SOCK

    [Install]
    WantedBy=default.target

After the above file has been created, enable the service:

.. code-block:: bash

   $ systemctl --user enable ssh-agent.service


python dev setup
~~~~~~~~~~~~~~~~~

* installed stow package

* ran the following commands:

  .. code-block:: bash

     # cd ~/.local/share/python-venv
     # python -m venv python3.6
     # python3.6/bin/pip install virtualenvwrapper
     # ln -s ~/.local/share/python-venv/python3.6/bin/virtualenvwrapper.sh ~/.local/bin
     # cd ~/.local/bin
     # cat <<EOF > pyenv.sh
     > #!/bin/sh
     > #
     > # pyenv.sh
     > exec python3.6 -m venv \$@
     > EOF
     # chmod +x pyenv.sh

zsh setup
~~~~~~~~~~

* cloned `https://github.com/arielmakestuff/zshconfig`_ to
  `~/.local/src/zshconfig`

* cloned `~/.local/src/zshconfig` to `~/.config/zsh`

* created `~/.config/zsh/lib`

* ran the following command:

  .. code-block:: bash

     # export ZPLUG_HOME=~/.config/zsh/lib/zplug

* cloned `https://github.com/zplug/zplug`_ to `~/.config/zsh/lib/zplug`

* ran the following commands:

  .. code-block:: bash

     # cd ~/.config/zsh
     # ln -s $(pwd)/zprofile ~/.zprofile
     # ln -s $(pwd)/zshenv ~/.zshenv
     # ln -s $(pwd)/zshrc ~/.zshrc

* ran `zsh` and answered `y` when asked if want to install plugins:

  .. code-block:: bash

     # zsh

* exited `zsh` and ran the following command to change the shell:

  .. code-block:: bash

     # chsh -s /bin/zsh


Todo
----

* Installed package `kmscon`

* Ran the following command:

  .. code-block:: bash

     ln -s /usr/lib/systemd/system/kmsconvt\@.service /etc/systemd/system/autovt\@.service

* Disabled `pam_securetty` module by commenting out the corresponding line in
  `/etc/pam.d/login`
