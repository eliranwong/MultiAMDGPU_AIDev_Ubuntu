# Install Ubuntu and Kernel

## Download Ubuntu Image, if necessary:

https://releases.ubuntu.com/jammy/

## Download Etcher for creating a bootable USB, 

https://etcher.balena.io/#download-etcher

## Upgrade to a supported kernel version, if necessary:

> sudo add-apt-repository ppa:cappelikan/ppa

> sudo apt update && sudo apt -y full-upgrade

> sudo apt install -y mainline

> mainline-gtk

Select version "6.6.32", for example, and click button "Install".

Restart to make changes effective:

> sudo reboot
