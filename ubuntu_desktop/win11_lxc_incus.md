# Prepare Tools

Install distrobuilder:

> sudo snap install distrobuilder --classic

Install virt-viewer:

> sudo apt install -y virt-viewer libguestfs-tools wimtools rsync

# Prepare Windows ISO

https://www.microsoft.com/software-download/windows11

Download Windows 11 Disk Image (ISO) for x64 devices > Windows 11 (multi-edition ISO for x64 devices) > English International > Confirm

Assuming the file is downloaded to ~/Downloads, as usual:

> cd ~/Downloads

> sudo distrobuilder repack-windows Win11_23H2_EnglishInternational_x64v2.iso win11.lxd.iso

# Setup lxc for the first time

Run the following command and follow instructions:

> lxc admin init

# Create Windows image and run

> lxc init win11 --vm --empty

> lxc config device override win11 root size=128GiB

> lxc config set win11 limits.cpu=8 limits.memory=32GiB

> lxc config device add win11 vtpm tpm path=/dev/tpm0

> lxc config device add win11 install disk source=$HOME/Downloads/win11.lxd.iso boot.priority=10

> lxc start win11 --console=vga

# Setup Windows

Follow instructions on the console for setup.

Attach to instance console every time after the Windows is restarted and terminal is closed till the whole setup is complete.

> lxc console win11 --type=vga

# Update VirtIO drivers

Download the latest VirtIO drivers and install it in Windows:

https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/virtio-win-guest-tools.exe

Diferent versions can be found at: https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/

# Dismount Windows ISO Image

> lxc config device remove win11 install

# Add alias

> echo "alias win11='lxc console win11 --type=vga&'" >> ~/.bashrc

# Enable Sound

Read https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/ubuntu_desktop/basic.md#replace-pulseaudio-with-pipewire-on-ubuntu-2204 for setting up pipewire

> sudo apt install -y pulseaudio pipewire-pulse libasound2-plugins

> lxc config set win11 raw.qemu -- "-device intel-hda -device hda-duplex -audio spice"

## Pulseaudio users

> sudo nano /etc/pulse/default.pa

Change from:

```
#load-module module-native-protocol-tcp
```

To:

```
load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1
```

## Pipewire-pulse users

> mkdir -p ~/.config/pipewire/pipewire-pulse.conf.d

> nano ~/.config/pipewire/pipewire-pulse.conf.d/pulse-tcp.conf

Add the following content:

```
context.exec = [
 { path = "pactl"  args = "load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1" }
]
```

## Export PULSE_SERVER

> echo "export PULSE_SERVER=tcp:127.0.0.1:4713" >> ~/.bashrc

# Reboot

> sudo reboot

# Run

> win11

# References

https://ubuntu.com/tutorials/how-to-install-a-windows-11-vm-using-lxd#1-overview

https://discuss.linuxcontainers.org/t/audio-via-pulseaudio-inside-container/8768

## Incus

https://ubuntuhandbook.org/index.php/2024/02/use-incus-container-ubuntu/

https://fostips.com/install-incus-container-ubuntu-debian/

https://discussion.scottibyte.com/t/windows-11-incus-virtual-machine/362

https://blog.simos.info/how-to-run-a-windows-virtual-machine-on-incus-on-linux/
