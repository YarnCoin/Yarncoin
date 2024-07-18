
Debian
====================
This directory contains files used to package yarncoind/yarncoin-qt
for Debian-based Linux systems. If you compile yarncoind/yarncoin-qt yourself, there are some useful files here.

## yarncoin: URI support ##


yarncoin-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install yarncoin-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your yarncoin-qt binary to `/usr/bin`
and the `../../share/pixmaps/yarncoin128.png` to `/usr/share/pixmaps`

yarncoin-qt.protocol (KDE)

