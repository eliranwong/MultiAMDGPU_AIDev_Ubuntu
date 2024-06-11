# Multipass

Created for testing:

> multipass launch 22.04 -c 4 -m 32G -d 128G -n primary

> multipass shell primary

Run in ```primary```:

> sudo apt update && sudo apt full-upgrade -y

> sudo modprobe snd-hda-intel

> echo "options snd-hda-intel model=generic" | sudo tee -a /etc/modprobe.d/alsa-base.conf

> sudo apt install ubuntu-desktop xrdp

> sudo passwd ubuntu

> remmina -c 'rdp://'$(multipass list | grep '^primary' | awk '{print $3}')

# Change Driver

https://multipass.run/docs/set-up-the-driver

> sudo snap connect multipass:libvirt

> multipass stop --all

> multipass set local.driver=libvirt

# Add Hardware

Add hardware via virt-manager:

> sudo apt virt-manager

> virt-manager

# References

https://multipass.run/install

https://multipass.run/docs/set-up-a-graphical-interface

https://stackoverflow.com/questions/67776548/how-to-fix-multipass-error-list-failed-cannot-connect-to-the-multipass-socket

https://askubuntu.com/questions/1406646/ubuntu-22-04-audio-output-not-working-dummy-audio?newreg=e9d320bcadec440da0a11d8a19469e37

https://discourse.ubuntu.com/t/how-to-downgrade-the-kernel-on-ubuntu-20-04-to-the-5-4-lts-version/26459