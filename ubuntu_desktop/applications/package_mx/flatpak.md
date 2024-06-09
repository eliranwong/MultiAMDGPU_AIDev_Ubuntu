# flatpak

To enable Flatpak:

1) Run on terminal:

> sudo apt install flatpak -y

> flatpak --user remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

2) Restart Linux virtual machine


To install a package:

> flatpak install [package-name]

> flatpak uninstall [package-name]

You may find flatpak packages at: https://flathub.org

Remarks: To add environment variable with flatpak, e.g. flatpak run --env=QT_QPA_PLATFORM=wayland APP [ARGUMENT?]
