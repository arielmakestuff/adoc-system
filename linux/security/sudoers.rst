#####
Sudo
#####

*************
Configuration
*************

Put the below in ``/etc/sudoers.d/local`` with user:group of ``root:root`` and
permissions of 644::

    Defaults	env_reset

    Cmnd_Alias POWER     =   /usr/bin/shutdown -h now, /usr/bin/halt, /usr/bin/poweroff, /usr/bin/reboot
    Cmnd_Alias STORAGE   =   /usr/bin/mount -o nosuid\,nodev\,noexec, /usr/bin/mount, /usr/bin/umount
    Cmnd_Alias SYSTEMD   =   /usr/bin/journalctl, /usr/bin/systemctl, /usr/bin/timedatectl
    Cmnd_Alias KILL      =   /usr/bin/kill, /usr/bin/killall
    Cmnd_Alias PKGMAN    =   /usr/bin/pacman,/usr/bin/paccache, /usr/bin/pkgfile, /usr/bin/rankmirrors
    Cmnd_Alias CFGMAN    =   /usr/bin/vim /etc/*, /usr/bin/vim /boot/*, /usr/bin/visudo
    Cmnd_Alias NETWORK   =   /usr/bin/netctl, /usr/bin/ethtool
    Cmnd_Alias FIREWALL  =   /usr/bin/iptables, /usr/bin/ip6tables
    Cmnd_Alias SHELL     =   /usr/bin/zsh, /usr/bin/bash
    %power	   ALL       =   (root)   NOPASSWD: POWER
    %network   ALL       =   (root)   NETWORK
    %storage   ALL       =   (root)   NOPASSWD: STORAGE
    root       ALL       =   (ALL)    ALL
    admin      ALL       =   (root)   NOPASSWD: SYSTEMD, NOPASSWD: KILL, NOPASSWD: FIREWALL
    pkgman     ALL       =   (root)   NOPASSWD: PKGMAN
    cfgman     ALL       =   (root)   NOPASSWD: CFGMAN
    smokybobo  ALL       =   (pkgman) SHELL, (cfgman) SHELL, (admin) SHELL
