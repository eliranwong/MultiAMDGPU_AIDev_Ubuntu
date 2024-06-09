# apt

To manage packages:

To show help of "apt" command:

> apt help

To display policy:

> apt policy

To update package information, upgrade installed packages and install necessary dependencies:

> sudo apt update && sudo apt dist-upgrade

To install a package ("-y" below is optional.  It simply says "yes".):

> sudo apt -y install [package]

To upgrade a package:

> sudo apt upgrade [package]

To upgrade a package and install new dependencies if necessary:

> sudo apt dist-upgrade [package]

To overwrite an installed old version without upgrading:

> sudo apt install [package] --no-upgrade

To remove a package without removing configuration files:

> sudo apt remove [package] && sudo apt autoremove

To remove a package together with configuration files:

> sudo apt purge [package]

To remove a package, unused packages, and configs:

> sudo apt --purge autoremove [package]

To remove old downloaded archive files:

> sudo apt autoclean

[apt autoclean removes the retrieved packages from the local cache only while the apt-get autoremove removes the unneeded packages that were once installed as a dependency.]

To list installed packages:

> apt list --installed

To check if a package or packages is/are installed, for examples:

> apt -qq list fcitx-frontend-qt5

> apt -qq list fcitx-frontend*

To list upgradable packages:

> apt list --upgradable

To show a package information:

> apt show [package]

TO show a package dependency:

> apt depends [package]

To search for a package, e.g.:

> apt search fcitx

OR

> apt-cache search fcitx
