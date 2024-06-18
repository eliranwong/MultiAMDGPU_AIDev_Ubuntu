To launch:

> lxc launch ubuntu:22.04 ub

or

> lxc launch ubuntu:22.04 ub -c limits.cpu=4 -c limits.memory=32GiB

To resize:

> lxc config device override ub root size=128GiB

To log in container:

> lxc exec ub -- sudo --login --user ubuntu

To set an alias:

> echo "alias ub='lxc exec ub -- sudo --login --user ubuntu'" >> ~/.bashrc

To setup:

> sudo apt update && sudo apt full-upgrade

> sudo apt install linux-modules-extra-`uname -r`

# Sound

For pipewire users:

In HOST:

To set up pipewire in HOST, read https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/ubuntu_desktop/basic.md#replace-pulseaudio-with-pipewire-on-ubuntu-2204

To grant network access:

> mkdir -p ~/.config/pipewire/pipewire-pulse.conf.d

> nano ~/.config/pipewire/pipewire-pulse.conf.d/pulse-tcp.conf

Add the following content:

```
context.exec = [
 { path = "pactl"  args = "load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1" }
]
```

> lxc config edit ub

Add the following content, under the `devices` section:

```
devices:
  Pulseovernetwork:
    bind: container #or instance
    connect: tcp:127.0.0.1:4713
    listen: tcp:127.0.0.1:4713
    type: proxy
```

Log in and run inside the CONTAINER:

> ub

> echo "export PULSE_SERVER=tcp:127.0.0.1:4713" >> ~/.bashrc

To test:

> sudo apt install espeak

> espeak testing

For pulseaudio users:

Read https://discuss.linuxcontainers.org/t/audio-via-pulseaudio-inside-container/8768

# GUI

In HOST:

Check display:

> echo $DISPLAY

Result, e.g.:

```
:1
```

> ls /tmp/.X11-unix/

Result, e.g.:

```
X1
```

Edit container profile:

> lxc config edit ub

Add the following content, under the `devices` section:

```
devices:
  X11Display:
    bind: container
    connect: unix:@/tmp/.X11-unix/X1
    listen: unix:@/tmp/.X11-unix/X1
    type: proxy
```

Configure X11's access control

> echo "xhost +local:" >> ~/.profile

> sudo reboot

Alternately, add ```security.uid``` and ```security.gid``` to X11Display device settings, i.e.:

```
devices:
  X11Display:
    bind: container
    connect: unix:@/tmp/.X11-unix/X1
    listen: unix:@/tmp/.X11-unix/X1
    security.uid: "1000"
    security.gid: "1000"
    type: proxy
```

Log in and run inside the CONTAINER:

> ub

> echo "export DISPLAY=:1" >> ~/.bashrc

Remarks: You may modify the number '1' according to your display settings.

To test:

> sudo apt install x11-apps

> xclock

# Map user [optional]

> printf "uid $(id -u) 1000\ngid $(id -g) 1000" | incus config set mycontainername raw.idmap -

# References

https://discuss.linuxcontainers.org/t/running-x11-software-in-incus-container/18180

https://discuss.linuxcontainers.org/t/incus-lxd-profile-for-gui-apps-wayland-x11-and-pulseaudio/18295

https://blog.swwomm.com/2022/08/lxd-containers-for-wayland-gui-apps.html

https://github.com/DevinNorgarb/gitbook-personnal-docs/blob/main/software-engineering/containerisation/lxc-lxd/how-to-easily-run-graphics-accelerated-gui-apps-in-lxd-containers-on-your-ubuntu-desktop.md