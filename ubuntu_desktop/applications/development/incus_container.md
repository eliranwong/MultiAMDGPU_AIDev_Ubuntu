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

# View available Images

To list all available images:

> incus image list images:

Filter available images, e.g.:

> incus image list images:ubuntu

> incus image list images:22.04

> incus image list images:debian

To filter images that work with ```cloud-init```:

> incus image list images:cloud

# Prepare a profile

> nano incus_profile_x11_audio

Add and save the content of either Profile 1 or 2:

Remarks: Check your display settings by running ```printenv | grep -i display``` and replace 'X1' with your display settings.

## Profile 1: X11 + PulseAudio

```
config:
  security.nesting: "true"
  cloud-init.user-data: |
    #cloud-config
    package_update: true
    package_upgrade: true
    package_reboot_if_required: true
    packages:
      - pulseaudio-utils
      - dbus-user-session
    write_files:
    - path: /var/lib/cloud/scripts/per-boot/set_up_sockets.sh
      permissions: 0755
      content: |
        #!/bin/bash
        user_name="ubuntu"
        user_uid=1000
        user_gid=1000
        if [[ -n ${user_name} ]]; then
          mnt_dir=/mnt/.container_sockets
          run_dir=/run/user/${user_uid}
          tmp_dir=/tmp/.X11-unix
          [[ ! -d "${tmp_dir}" ]] && mkdir -p "${tmp_dir}" && chmod 777 "${tmp_dir}"
          [[ ! -d "${run_dir}" ]] && mkdir -p "${run_dir}" && chmod 700 "${run_dir}" && chown ${user_uid}:${user_gid} "${run_dir}"
          [[ -S "${mnt_dir}/X1" ]] && [[ -d "${tmp_dir}" ]] && [[ ! -e "${tmp_dir}/X1" ]] && touch "${tmp_dir}/X1" && sudo mount --bind "${mnt_dir}/X1" "${tmp_dir}/X1"
          [[ ! -d "${run_dir}/pulse" ]] && mkdir -p "${run_dir}/pulse" && chmod 700 "${run_dir}/pulse" && chown ${user_uid}:${user_gid} "${run_dir}/pulse"
          [[ -S "${mnt_dir}/native" ]]  && [[ -d "${run_dir}/pulse" ]] && [[ ! -e "${run_dir}/pulse/native" ]] && touch "${run_dir}/pulse/native" && sudo mount --bind "${mnt_dir}/native" "${run_dir}/pulse/native"
        fi
    - path: /var/lib/cloud/scripts/per-once/set_up_env_vars.sh
      permissions: 0755
      content: |
        #!/bin/bash
        user_uid=1000
        home_dir="$( getent passwd ${user_uid} | cut -d: -f6 )"
        profile="${home_dir}/.profile"
        if [[ -f "${profile}" ]]; then
          echo "export DISPLAY=:1" >> "${profile}"
          echo "export NO_AT_BRIDGE=1" >> "${profile}"
        fi
    - path: /var/lib/cloud/scripts/per-once/change_gid.sh
      permissions: 0755
      content: |
        #!/bin/bash
        user_name="ubuntu"
        user_uid=1000
        user_gid=1000
        home_dir=$( getent passwd ${user_name} | cut -d: -f6 )
        if [[ -n ${user_name} && ! ${user_uid} == ${user_gid} ]]; then
          group_to_move=$( getent group ${user_uid} | cut -d: -f1 )
          if [[ -n ${group_to_move} ]]; then
            for gid in {1000..6000}; do
              return_value=$( getent group ${gid} )
              if [[ -z ${return_value} ]]; then
                groupmod -g ${gid} ${group_to_move}
                break
              fi
            done
          fi
          users_group=$( getent group ${user_gid} | cut -d: -f1 )
          groupmod -g ${user_uid} ${users_group}
          chown -R ${user_uid}:${user_uid} "${home_dir}"
        fi
description: GUI X11 profile with pulseaudio
devices:
  x11_socket:
    source: /tmp/.X11-unix/X1
    path: /mnt/.container_sockets/X1
    type: disk
  pulseaudio_socket:
    type: disk
    shift: true
    source: /run/user/1000/pulse/native
    path: /mnt/.container_sockets/native
```

## Profile: X11 + PulseAudio + Pipewire

```
config:
  security.nesting: "true"
  cloud-init.user-data: |
    #cloud-config
    package_update: true
    package_upgrade: true
    package_reboot_if_required: true
    packages:
      - pulseaudio-utils
      - dbus-user-session
    write_files:
    - path: /var/lib/cloud/scripts/per-boot/set_up_sockets.sh
      permissions: 0755
      content: |
        #!/bin/bash
        user_name="ubuntu"
        user_uid=1000
        user_gid=1000
        if [[ -n ${user_name} ]]; then
          mnt_dir=/mnt/.container_sockets
          run_dir=/run/user/${user_uid}
          tmp_dir=/tmp/.X11-unix
          [[ ! -d "${tmp_dir}" ]] && mkdir -p "${tmp_dir}" && chmod 777 "${tmp_dir}"
          [[ ! -d "${run_dir}" ]] && mkdir -p "${run_dir}" && chmod 700 "${run_dir}" && chown ${user_uid}:${user_gid} "${run_dir}"
          [[ -S "${mnt_dir}/X1" ]] && [[ -d "${tmp_dir}" ]] && [[ ! -e "${tmp_dir}/X1" ]] && touch "${tmp_dir}/X1" && sudo mount --bind "${mnt_dir}/X1" "${tmp_dir}/X1"
          [[ ! -d "${run_dir}/pulse" ]] && mkdir -p "${run_dir}/pulse" && chmod 700 "${run_dir}/pulse" && chown ${user_uid}:${user_gid} "${run_dir}/pulse"
          [[ -S "${mnt_dir}/native" ]]  && [[ -d "${run_dir}/pulse" ]] && [[ ! -e "${run_dir}/pulse/native" ]] && touch "${run_dir}/pulse/native" && sudo mount --bind "${mnt_dir}/native" "${run_dir}/pulse/native"
          [[ -S "${mnt_dir}/pipewire-0" && -d "${run_dir}" && ! -e "${run_dir}/pipewire-0" ]] && touch "${run_dir}/pipewire-0" && sudo mount --bind "${mnt_dir}/pipewire-0" "${run_dir}/pipewire-0"
          [[ -S "${mnt_dir}/pipewire-0-manager" && -d "${run_dir}" && ! -e "${run_dir}/pipewire-0-manager" ]] && touch "${run_dir}/pipewire-0-manager" && sudo mount --bind "${mnt_dir}/pipewire-0-manager" "${run_dir}/pipewire-0-manager"
        fi
    - path: /var/lib/cloud/scripts/per-once/set_up_env_vars.sh
      permissions: 0755
      content: |
        #!/bin/bash
        user_uid=1000
        home_dir="$( getent passwd ${user_uid} | cut -d: -f6 )"
        profile="${home_dir}/.profile"
        if [[ -f "${profile}" ]]; then
          echo "export DISPLAY=:1" >> "${profile}"
          echo "export NO_AT_BRIDGE=1" >> "${profile}"
        fi
    - path: /var/lib/cloud/scripts/per-once/change_gid.sh
      permissions: 0755
      content: |
        #!/bin/bash
        user_name="ubuntu"
        user_uid=1000
        user_gid=1000
        home_dir=$( getent passwd ${user_name} | cut -d: -f6 )
        if [[ -n ${user_name} && ! ${user_uid} == ${user_gid} ]]; then
          group_to_move=$( getent group ${user_uid} | cut -d: -f1 )
          if [[ -n ${group_to_move} ]]; then
            for gid in {1000..6000}; do
              return_value=$( getent group ${gid} )
              if [[ -z ${return_value} ]]; then
                groupmod -g ${gid} ${group_to_move}
                break
              fi
            done
          fi
          users_group=$( getent group ${user_gid} | cut -d: -f1 )
          groupmod -g ${user_uid} ${users_group}
          chown -R ${user_uid}:${user_uid} "${home_dir}"
        fi
description: GUI X11 profile with pulseaudio+pipewire
devices:
  x11_socket:
    source: /tmp/.X11-unix/X1
    path: /mnt/.container_sockets/X1
    type: disk
  pulseaudio_socket:
    type: disk
    shift: true
    source: /run/user/1000/pulse/native
    path: /mnt/.container_sockets/native
  pipewire_socket:
    type: disk
    shift: true
    source: /run/user/1000/pipewire-0
    path: /mnt/.container_sockets/pipewire-0
  pipewire_manager_socket:
    type: disk
    shift: true
    source: /run/user/1000/pipewire-0-manager
    path: /mnt/.container_sockets/pipewire-0-manager
```

# Create a Profile

To create a new profile:

> incus profile create x11_audio < incus_profile_x11_audio

To edit an existing profile:

> incus profile edit x11_audio < incus_profile_x11_audio

Configure X11's access control, to allow non-network local connections:

> xhost +local:

> echo "xhost +local:" >> ~/.profile

# Launch

To launch a container:

> incus launch images:ubuntu/jammy/cloud -p default -p x11_audio ub

To launch a container with limits, e.g.:

> incus launch images:ubuntu/jammy/cloud -p default -p x11_audio ub -c limits.cpu=4 -c limits.memory=32GiB

To edit size

> incus config device override ub root size=128GiB

# GPU Support

Check available GPUs:

> incus info --resources | grep "PCI address: " -B 4

iGPU ONLY:

```
devices:
  gpu:
    type: gpu
    gid: 44
    pci: "xxxx:xx:xx.x"
```

Multiple GPUs:

```
devices:
  gpu1:
    type: gpu
    gid: 44
    pci: "xxxx:xx:xx.x"
  gpu2:
    type: gpu
    gid: 44
    pci: "xxxx:xx:xx.x"
```

Remarks:

1. Replace "xxxx:xx:xx.x" with your GPU pci addresses.
2. To work with ROCM, add either iGPU or discrete GPUs.

# Login

> incus exec ub -- sudo --login --user ubuntu

# Test Sound

> sudo apt install -y espeak

> espeak test

# Test Display

> sudo apt install -y x11-apps

> xclock

# Webcam

Run in HOST:

> incus info --resources | grep Webcam -B 3 -A 3

Output, e.g.:

```
  Device 1:
    Vendor: Logitech, Inc.
    Vendor ID: 046d
    Product: Logitech Webcam C930e
    Product ID: 0843
    Bus Address: 3
    Device Address: 2
```

> ls -l /dev/snd/by-id

Output, e.g.:

```
usb-046d_Logitech_Webcam_C930e_095C816E-02 -> ../controlC2
```

> incus config edit ub

```
devices:
  Webcam:
    busnum: "3"
    devnum: "2"
    gid: "1000"
    productid: "0843"
    type: usb
    uid: "1000"
    vendorid: 046d
  controlC2:
    source: /dev/snd/controlC2
    type: unix-char
  video0:
    source: /dev/video0
    gid: 44
    type: unix-char
  video1:
    source: /dev/video1
    gid: 44
    type: unix-char
```

# Test Camera

> sudo usermod -a -G render,video $LOGNAME

> sudo apt install build-essential linux-headers-`uname -r` guvcview

> sudo reboot

> guvcview

# Test Microphone

Run in CONTAINER:

> sudo apt install -y alsa-base alsa-utils pavucontrol

Run in HOST:

> incus restart ub

Run in CONTAINER:

> pavucontrol

Go to "Input Devices" and say something ...

# Install python and related tools

> sudo apt install -y make build-essential python3 python-setuptools python3-pip python3-dev python3-venv libssl-dev libffi-dev libnss3 zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev nano micro

# Test Speech Recognition

> sudo apt install -y portaudio19-dev

> python3 -m venv test

> source test/bin/activate

> pip install sounddevice SpeechRecognition PyAudio

> nano test.py

```
import sounddevice
import speech_recognition as sr
with sr.Microphone() as source:
  r = sr.Recognizer()
  audio = r.listen(source)
  print(r.recognize_google(audio, language="en-US"))
```

> python3 test.py

Say something ...

# Resources

incus info --resources

To add a device, read https://linuxcontainers.org/incus/docs/main/reference/devices/

# Delete

To delete a container:

> incus delete mycontainername

# Uninstall

> sudo apt remove --autoremove incus incus-base

> sudo rm /etc/apt/sources.list.d/zabbly-incus-stable.sources /etc/apt/keyrings/zabbly.asc

# References

https://linuxcontainers.org/incus/docs/main/

https://ubuntuhandbook.org/index.php/2024/02/use-incus-container-ubuntu/

https://discuss.linuxcontainers.org/t/incus-lxd-profile-for-gui-apps-wayland-x11-and-pulseaudio/18295/2

https://xahteiwi.eu/resources/hints-and-kinks/lxc-basics/

https://discourse.ubuntu.com/t/lxd-incus-profile-for-gui-apps-wayland-x11-and-pulseaudio/40255

https://discuss.linuxcontainers.org/t/how-to-add-sound-to-an-ubuntu-lxd-vm/14372

https://discuss.linuxcontainers.org/t/audio-via-pulseaudio-inside-container/8768

https://stackoverflow.com/questions/45700653/can-my-docker-container-app-access-the-hosts-microphone-and-speaker-mac-wind

https://askubuntu.com/questions/508221/sound-input-device-microphone-not-working