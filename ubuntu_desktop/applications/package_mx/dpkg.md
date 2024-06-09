# dpkg

To check architecture:

> dpkg --print-architecture

To install a package:

> dpkg -i [package-name]

To remove an already installed package:

> dpkg -r [package-name]

To remove an already installed package, together with its configurations:

> dpkg -P [package-name]

To list all installed packages:

> pkg -l

To make dpkg list contents of a package

> dpkg --contents [package name]

To unpack a package without configurations:

> dpkg --unpack [package-name]

To configure an unpacked package:

> dpkg --configure [package-name]

To check if a package is installed:

> dpkg -s [package-name]
