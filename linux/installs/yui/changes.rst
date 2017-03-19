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
     Address=192.168.1.7/24
     Gateway=192.168.1.1
     DNS=192.168.1.1

* Start and enable the following services:

  1. `systemd-networkd.service`

  2. `systemd-resolved.service`

* Rename `/etc/resolv.conf` to `/etc/resolv.conf.orig`, and then run the
  following:

  .. code-block:: bash

     # ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

sudo
----

Switch to the root account and create a new file in `/etc/sudoers.d`:

.. code-block:: bash

   % su
   Password:
   # visudo -f /etc/sudoers.d/local



Normal user
-----------

Ran the following command:

.. code-block:: bash

   # useradd -m -s /bin/bash smokybobo


Home directory
--------------

python dev setup
~~~~~~~~~~~~~~~~~

* created the following directories:

  1. `~/.local/share/python-venv`

  2. `~/.local/stow`

  3. `~/.local/bin`

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

* created the following directories:

  1. `~/.config`

  2. `~/.cache`

  3. `~/.local/share`

  4. `~/.local/stow`

  5. `~/.local/src`

  6. `~/src`

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
