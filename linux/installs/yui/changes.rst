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

   # for u in smokybobo admin cfgman pkgman ; do useradd -m -s /bin/bash $u ; done

The admin user account will be used for `journalctl`, `systemctl`, `mount`,
`kill`, and `iptables`.

The `cfgman` user will be used to edit files in `/etc`.

The `pkgman` user will be used to install packages via `pacman`.

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

4. `~/.local/share`

5. `~/.local/stow`

6. `~/.local/src`

7. `~/.config`

8. `~/src`

9. `~/.cache`

10. `~/.cache/archbuild`

11. `~/.cache/tmp`

12. `~/.ssh`

13. `~/.config/systemd/user`

14. `~/media`

15. `~/media/music`

16. `~/.config/alsa`


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

* assumes ssh setup and user's ssh public key is on github already

* cloned `https://github.com/arielmakestuff/zshconfig`_ to
  bare repo `~/.local/src/zshconfig.git`:

  .. code-block:: bash

     $ cd ~/.local/src
     $ git clone --bare git@github.com:arielmakestuff/zshconfig

* made sure to switch default branch to develop:

  .. code-block:: bash

     $ cd ~/.local/src/zshconfig.git
     $ git symbolic-ref HEAD refs/heads/develop

* cloned `~/.local/src/zshconfig` to `~/.config/zsh`:

  .. code-block:: bash

     $ git clone ~/.local/src/zshconfig ~/.config/zsh

* ran zplug install and installed zsh config files

  .. code-block:: bash

     $ cd ~/.config/zsh
     $ bin/install  # zplug install
     $ ln -s $(pwd)/zprofile ~/.zprofile
     $ ln -s $(pwd)/zshenv ~/.zshenv
     $ ln -s $(pwd)/zshrc ~/.zshrc

* ran `zsh` and answered `y` when asked if want to install plugins:

  .. code-block:: bash

     # zsh

* exited `zsh` and ran the following command to change the shell:

  .. code-block:: bash

     # chsh -s /bin/zsh


Sound
~~~~~

With many different sound devices, need to configure the default. Note: the
`alsa-utils` package needs to be installed.

To see a list of sound card devices:

.. code-block:: bash

   $ aplay -l
   **** List of PLAYBACK Hardware Devices ****
   card 0: Intel [HDA Intel], device 0: ALC889A Analog [ALC889A Analog]
     Subdevices: 1/1
     Subdevice #0: subdevice #0
   card 0: Intel [HDA Intel], device 1: ALC889A Digital [ALC889A Digital]
     Subdevices: 1/1
     Subdevice #0: subdevice #0
   card 1: HDMI [HDA ATI HDMI], device 3: HDMI 0 [HDMI 0]
     Subdevices: 1/1
     Subdevice #0: subdevice #0
   card 1: HDMI [HDA ATI HDMI], device 7: HDMI 1 [HDMI 1]
     Subdevices: 1/1
     Subdevice #0: subdevice #0
   card 1: HDMI [HDA ATI HDMI], device 8: HDMI 2 [HDMI 2]
     Subdevices: 1/1
     Subdevice #0: subdevice #0
   card 1: HDMI [HDA ATI HDMI], device 9: HDMI 3 [HDMI 3]
     Subdevices: 1/1
     Subdevice #0: subdevice #0
   card 1: HDMI [HDA ATI HDMI], device 10: HDMI 4 [HDMI 4]
     Subdevices: 1/1
     Subdevice #0: subdevice #0
   card 1: HDMI [HDA ATI HDMI], device 11: HDMI 5 [HDMI 5]
     Subdevices: 1/1
     Subdevice #0: subdevice #0

To get a list of PCMs:

.. code-block:: bash

   $ aplay -L | grep :CARD
   sysdefault:CARD=Intel
   front:CARD=Intel,DEV=0
   surround21:CARD=Intel,DEV=0
   surround40:CARD=Intel,DEV=0
   surround41:CARD=Intel,DEV=0
   surround50:CARD=Intel,DEV=0
   surround51:CARD=Intel,DEV=0
   surround71:CARD=Intel,DEV=0
   iec958:CARD=Intel,DEV=0
   dmix:CARD=Intel,DEV=0
   dmix:CARD=Intel,DEV=1
   dsnoop:CARD=Intel,DEV=0
   dsnoop:CARD=Intel,DEV=1
   hw:CARD=Intel,DEV=0
   hw:CARD=Intel,DEV=1
   plughw:CARD=Intel,DEV=0
   plughw:CARD=Intel,DEV=1
   hdmi:CARD=HDMI,DEV=0
   hdmi:CARD=HDMI,DEV=1
   hdmi:CARD=HDMI,DEV=2
   hdmi:CARD=HDMI,DEV=3
   hdmi:CARD=HDMI,DEV=4
   hdmi:CARD=HDMI,DEV=5
   dmix:CARD=HDMI,DEV=3
   dmix:CARD=HDMI,DEV=7
   dmix:CARD=HDMI,DEV=8
   dmix:CARD=HDMI,DEV=9
   dmix:CARD=HDMI,DEV=10
   dmix:CARD=HDMI,DEV=11
   dsnoop:CARD=HDMI,DEV=3
   dsnoop:CARD=HDMI,DEV=7
   dsnoop:CARD=HDMI,DEV=8
   dsnoop:CARD=HDMI,DEV=9
   dsnoop:CARD=HDMI,DEV=10
   dsnoop:CARD=HDMI,DEV=11
   hw:CARD=HDMI,DEV=3
   hw:CARD=HDMI,DEV=7
   hw:CARD=HDMI,DEV=8
   hw:CARD=HDMI,DEV=9
   hw:CARD=HDMI,DEV=10
   hw:CARD=HDMI,DEV=11
   plughw:CARD=HDMI,DEV=3
   plughw:CARD=HDMI,DEV=7
   plughw:CARD=HDMI,DEV=8
   plughw:CARD=HDMI,DEV=9
   plughw:CARD=HDMI,DEV=10
   plughw:CARD=HDMI,DEV=11


To set the default card to the HDA Intel, create `~/.config/alsa/asoundrc`
with the following contents::

    # Set default device and control (see aplay -l output)
    defaults.ctl.card 0
    defaults.pcm.card 0
    defaults.pcm.device 0

Finally, symlink the config to `$HOME`:

.. code-block:: bash

   $ ln -s ~/.config/alsa/asoundrc ~/.asoundrc

To test the configuration, log out and log back in, and then run the following
command. White noise should be heard:

.. code-block:: bash

   $ speaker-test -c 2


Todo
----

* Installed package `kmscon`

* Ran the following command:

  .. code-block:: bash

     ln -s /usr/lib/systemd/system/kmsconvt\@.service /etc/systemd/system/autovt\@.service

* Disabled `pam_securetty` module by commenting out the corresponding line in
  `/etc/pam.d/login`


* instructions on installing `cower` and `pacaur` from the AUR

* zsh start and shutdown file order

* contents of `/etc/sudoers.d/local` file
