# Multipass

Created for testing:

> multipass launch 22.04 -c 4 -m 32G -d 128G -n primary

> multipass shell primary

Run in ```primary```:

> sudo apt update && sudo apt full-upgrade -y

> sudo apt install linux-modules-extra-`uname -r`

sudo apt install linux-modules-extra-5.15.0-112-generic

echo "options snd-hda-intel model=generic" | sudo tee -a /etc/modprobe.d/alsa-base.conf

> sudo apt install ubuntu-desktop xrdp

> sudo passwd ubuntu

> remmina -c 'rdp://'$(multipass list | grep '^primary' | awk '{print $3}')

https://multipass.run/install

https://multipass.run/docs/set-up-a-graphical-interface