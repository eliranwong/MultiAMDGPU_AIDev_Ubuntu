To launch:

> lxc launch ubuntu:22.04 ub

or

> lxc launch ubuntu:22.04 ub -c limits.cpu=4 -c limits.memory=32GiB

To resize:

> lxc config device override ub root size=128GiB

To log in container:

> lxc exec ub -- sudo --login --user ubuntu

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

In CONTAINER:

> echo "export PULSE_SERVER=tcp:127.0.0.1:4713" >> ~/.bashrc

To test:

> sudo apt install espeak

> espeak testing

For pulseaudio users:

Read https://discuss.linuxcontainers.org/t/audio-via-pulseaudio-inside-container/8768