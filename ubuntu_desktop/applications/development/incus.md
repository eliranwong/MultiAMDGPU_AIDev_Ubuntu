# Install

> sudo mkdir -p /etc/apt/keyrings

> wget -qO - https://pkgs.zabbly.com/key.asc | sudo tee /etc/apt/keyrings/zabbly.asc

> sudo nano /etc/apt/sources.list.d/zabbly-incus-stable.sources

Add the following content:

```
Enabled: yes
Types: deb
URIs: https://pkgs.zabbly.com/incus/stable
Suites: jammy
Components: main
Architectures: amd64
Signed-By: /etc/apt/keyrings/zabbly.asc
```

> sudo apt update

> sudo apt install -y incus

> sudo adduser $LOGNAME incus-admin

> newgrp incus-admin

> incus admin init

# Images

To list all available images:

> incus image list images:

Filter available images, e.g.:

> incus image list images:ubuntu

> incus image list images:22.04

> incus image list images:debian

# Operate

To launch a container:

> incus launch ubuntu:22.04 ub --vm -c limits.cpu=4 -c limits.memory=32GiB

> incus config device override ub root size=128GiB

> incus config set ub raw.qemu -- "-device intel-hda -device hda-duplex -audio spice"

To get into container bash shell

> incus exec mycontainername bash

# Map user

> printf "uid $(id -u) 1000\ngid $(id -g) 1000" | incus config set mycontainername raw.idmap -

# Resources

incus info --resources

# Delete

To delete a container:

> incus delete mycontainername

# Uninstall

> sudo apt remove --autoremove incus incus-base

> sudo rm /etc/apt/sources.list.d/zabbly-incus-stable.sources /etc/apt/keyrings/zabbly.asc

# References

https://linuxcontainers.org/incus/docs/main/

https://ubuntuhandbook.org/index.php/2024/02/use-incus-container-ubuntu/

https://xahteiwi.eu/resources/hints-and-kinks/lxc-basics/

https://discourse.ubuntu.com/t/lxd-incus-profile-for-gui-apps-wayland-x11-and-pulseaudio/40255

https://discuss.linuxcontainers.org/t/incus-lxd-profile-for-gui-apps-wayland-x11-and-pulseaudio/18295/2

https://discuss.linuxcontainers.org/t/how-to-add-sound-to-an-ubuntu-lxd-vm/14372

https://discuss.linuxcontainers.org/t/audio-via-pulseaudio-inside-container/8768