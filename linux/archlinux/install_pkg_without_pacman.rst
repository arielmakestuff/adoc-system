Reinstall a package with a broken pacman
========================================

Assuming you have the package file ``${pkg}.pkg.tar.gz``, simply extract this
folder onto the root directory::

    tar -xvpf ${pkg} -C / --exclude .PKGINFO --exclude .INSTALL
