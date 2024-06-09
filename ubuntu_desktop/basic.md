# Introduction

Notes on basic setup of Ubuntu Desktop 22.0.4 LTS

# Upgrade

> sudo apt update && sudo apt full-upgrade

# Basic Tools & Libraries

> sudo apt update

> sudo apt -y install software-properties-common dirmngr apt-transport-https lsb-release ca-certificates apt-utils build-essential make cmake tree wget curl git zip unzip xz-utils nano micro w3m lynx sqlite3 libsqlite3-dev libnss3 libnss3-dev libgl1-mesa-dev mesa-utils libglu1-mesa lsb-release binutils ffmpeg gawk opencc plocate gnome-keyring libssl-dev libffi-dev libpci3 libpci-dev python3 python3-setuptools python3-pip python3-dev python3-venv zlib1g-dev libgdbm-dev libreadline-dev libbz2-dev gcc xorg-dev exo-utils dex xdg-utils libavcodec-extra libportaudio2 moreutils llvm tk-dev liblzma-dev python3-openssl libxml2-dev libxmlsec1-dev protobuf-compiler libc6-dev libstdc++-12-dev libxcb-cursor-dev libxcb-cursor0 libncurses-dev libncurses6 ubuntu-restricted-addons ubuntu-restricted-extras gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly xsel portaudio19-dev vlc

# Upgrade cmake

e.g. Download 'cmake-3.29.3-linux-x86_64.sh' from https://cmake.org/download/:

> bash cmake-3.29.3-linux-x86_64.sh

# Pyenv

> curl https://pyenv.run | bash

(edit file '.bashrc' with an editor of your choice. we use 'micro' in example below)

> micro .bashrc

copy the following lines at the end of the file:

```
# pyenv
# Load pyenv automatically by appending
# the following to 
# ~/.bash_profile if it exists, otherwise ~/.profile (for login shells)
# and ~/.bashrc (for interactive shells) :
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
# Restart your shell for the changes to take effect.
# Load pyenv-virtualenv automatically by adding
# the following to ~/.bashrc:
eval "$(pyenv virtualenv-init -)"
# shims path
export PATH="$PYENV_ROOT/shims:$PATH"
```

To check available versions, e.g.

> pyenv install --list | grep 3.10.1

To install a version:

> pyenv install 3.10.14

Remarks: [onnxruntime-rocm](https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu#onnx-runtime-with-rocm) has a wheel that supports python version 3.10

To set a version global:

> pyenv global 3.10.14

To set a local version:

> cd my_project

> pyenv local 3.10.14

More about pyenv at: https://github.com/pyenv/pyenv/wiki

More about pyenv-virtualenv at: https://github.com/pyenv/pyenv-virtualenv

More about pyenv plugins at: https://github.com/pyenv/pyenv/wiki/Plugins

# Python packages

> pip install --upgrade pip

> pip install wheel twine

# Install ROCm

https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu#install-rocm-602

> echo "export PATH=$HOME/.local/bin:/opt/rocm-6.0.2/bin:/opt/rocm-6.0.2/llvm/bin:$PATH" >> ~/.bashrc

# Install Common Fonts

> sudo apt install -y fonts-dejavu* fonts-liberation* ttf-mscorefonts-installer fonts-crosextra-* fonts-takao-gothic fonts-opensymbol

# Install Google Fonts

> wget https://github.com/google/fonts/archive/master.tar.gz -O gf.tar.gz

> sudo tar -xf gf.tar.gz --directory /usr/share

> sudo chown -R :users /usr/share/fonts-main

> sudo mkdir -p /usr/share/fonts/truetype/google-fonts

> sudo find /usr/share/fonts-main/ -name "*.ttf" -exec install -m644 {} /usr/share/fonts/truetype/google-fonts/ \\; || return 1

> rm -f gf.tar.gz

> sudo fc-cache -f && sudo rm -rf /var/cache/*

# Add Fonts with font-viewer

> sudo apt install -y font-viewer font-manager-common

For example:

1. download Hebrew fonts from https://www.1001fonts.com/hebrew-fonts.html
2. unzip the downloaded zip files
3. double click a font file in pcmanfm to launch '/usr/libexec/font-manager/font-viewer'
4. press 'install font'

# Install Custom Fonts [optional]

To install custom fonts, e.g.

```
mkdir ~/.fonts
cp -r ~/Downloads/CustomFonts/ ~/.fonts/
fc-cache -f -v
```

# Install Chinese Fonts [Optional]

> sudo apt install -y xfonts-wqy ttf-wqy-zenhei ttf-wqy-microhei fonts-arphic-bkai00mp fonts-arphic-bsmi00lp fonts-arphic-gbsn00lp fonts-arphic-gkai00mp xfonts-intl-chinese xfonts-intl-chinese-big

# Set up ibus Chinese Input [Optional]

Settings > Language and Region > Manage Installed Languages > Install / Remove Languages > "Chinese (Simplified)" and "Chinese (Traditional)"

> sudo locale-gen

> sudo apt install ibus-pinyin

> ibus restart

> ibus-setup

Settings > Keyboard > Input Sources > Add > Chinese (Intelligent Pinyin) > Preferences > General > Chinese > Traditional

> nano ~/.bashrc

export LC_CTYPE=zh_CN.UTF-8<br>
export XIM=ibus<br>
export XIM_PROGRAM=/usr/bin/ibus<br>
export QT_IM_MODULE=ibus<br>
export GTK_IM_MODULE=ibus<br>
export XMODIFIERS=@im=ibus<br>
export DefaultIMModule=ibus

# Connect Android phone

Android phone:

Install and run 'KDE Connect'; select a folder and grant permission for file sharing

On Ubuntu, run either:

> sudo apt install gnome-shell-extension-gsconnect gnome-shell-extension-manager

Related packages: kdeconnect nautilus-kdeconnect

Launch Gnome Shell Extension Manager to enable 'GSConnect'

Pair Android phone with Ubuntu via 'KDE Connect' on Android phone.

# Switch to Xorg Session

Ubuntu 22.04 uses wayland by default, switch to Xorg Session to improve application compatablility.

> sudo nano /etc/gdm3/custom.conf

Uncomment the line "#WaylandEnable=false" by removing the "#" sign.

Note: moving window programmatically in Qt applications do not work on wayland, read https://forum.qt.io/topic/142043/pyside6-qmainwindow-move-not-working-on-ubuntu-22-04  Switch to Xorg session to work around the issue.

Do not use the touchegg package provided by Ubuntu.  It does not work with X11 gestures.

Instaall X11 gestures:

> sudo add-apt-repository ppa:touchegg/stable

enter sudo password

press "Enter"

> sudo apt update

> sudo apt install touchegg

To verify,

> systemctl status touchegg.service

Launch gnome shell extension manager > Browser > "X11 gestures"

Click "Install"

Read more about hand gestures on X11 at https://ubuntuhandbook.org/index.php/2022/06/touchpad-gestures-ubuntu-22-04-xorg/

# Useful Gnome Shell Extension

* GSConnect
* Clipboard Indicator
* Vitals
* Caffeine
* Apps Menu
* Places Status Indicator
* Removable Drive Menu
* Hide Activities Button
* Improved Workspace Indicator
* Impatience
* Just perfection
* CPU Power Manager
* Sound input & output device chooser
* X11 gestures

# Work with File Selection

## Example - Microsoft Visual Studio Code

Create a script file:

> nano ~/.local/share/nautilus/scripts/Code

Add the following content:

```
#!/usr/bin/env bash
# Get the selected file or folder path
path="$NAUTILUS_SCRIPT_SELECTED_FILE_PATHS"
path=$(echo "$path" | sed -e 's/^[[:space:]]*//' -e 's/[[:space:]]*$//')
code "$path"
```

Add mode:

> chmod +x ~/.local/share/nautilus/scripts/Code

## Example - Annotator

Create a script file:

> nano ~/.local/share/nautilus/scripts/Annotator

Add the following content:

```
#!/usr/bin/env bash
# Get the selected file or folder path
path="$NAUTILUS_SCRIPT_SELECTED_FILE_PATHS"
path=$(echo "$path" | sed -e 's/^[[:space:]]*//' -e 's/[[:space:]]*$//')
com.github.phase1geo.annotator "$path"
```

Add mode:

> chmod +x ~/.local/share/nautilus/scripts/Annotator

# FreeGenius AI

To install:

```
sudo apt update && sudo apt install -y ffmpeg portaudio19-dev vlc
mkdir -p ~/apps
cd ~/apps
python3 -m venv freegenius
source freegenius/bin/activate
pip install freegenius piper-tts
echo "export PATH=$HOME/apps/freegenius/bin:$PATH"
```

## GPU Acceleration

Read https://github.com/eliranwong/freegenius/wiki/Speed-Up-with-GPU-Acceleration

## Startup Applications Preferences

Add a startup application preference:

1. Press ```super``` key and search startup to launch the 'Startup Applications Preferences' Window
2. Add a new entry

```Name``` FreeGenius AI Hub
```Command``` env HSA_OVERRIDE_GFX_VERSION=10.3.0 /home/eliran/apps/freegenius/bin/python3 /home/eliran/apps/freegenius/bin/freegeniushub
```Comment``` FreeGenius AI Hub

![startup](https://github.com/eliranwong/ubuntu_on_surface_go/assets/25262722/579507f7-aeda-4021-b784-c0c8232fc3a2)

## Work with text selection

Install xsel

> apt install xsel

Add custom keyboard shortcuts:

1. Launch "Settings"
2. Go to "Keyboard" > "Keyboard Shortcuts" > "View and Customise Shortcuts" > "Custom Shortcuts"
3. Select "+" to add a custom shortcut and enter the following information, e.g.:

For example,
```
Name: FreeGenius AI
Command: /usr/bin/gnome-terminal --command home/username/freegenius/FreeGenius
Shortcut: Ctrl + Alt + L
```
Remarks: Change ```username``` to your username.

![text_selection_keyboard_shortcut](https://github.com/eliranwong/freegenius/assets/25262722/08d0899c-fbc7-4696-b341-b9b09fd67121)

## Work with file selection

FreeGenius AI v0.2.74+ automatically generates a script at ~/.local/share/nautilus/scripts, which work with file or folder selection by right-clicking.

# Set up office 365 Email

> sudo apt update && sudo apt dist-upgrade

> sudo apt install -y evolution evolution-ews

> evolution

1) New > Mail Account, enter "Full Name" and "Email Address"

2) uncheck "Look up mail server details based on the entered email address" and click "Next"

3) Server Type > Exchange Web Services > Host URL, enter:

> https://outlook.office365.com/EWS/Exchange.asmx

4) Authentication > Click "Check for Supported Types" and Keep "OAuth2 (Office365)"

5) Click "Fetch URL"

6) Enter username and password for authentication

7) Next > Next > Next > Apply

Alternately, read https://sites.utexas.edu/glenmark/2021/02/01/how-to-setup-your-office-365-email-using-evolution-ews-linux/#:~:text=Configuring%20Evolution%2DEWS%20to%20connect%20to%20Exchange%20Online&text=Enter%20your%20name%20and%20your,%2FEWS%2FExchange.asmx%20.

# Youtube Downloader

To install:

> wget -P ~/.local/bin/ https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp

To download mp3 audio:

> yt-dlp -x --audio-format mp3 [youtube link]

e.g. 

> yt-dlp -x --audio-format mp3 https://www.youtube.com/watch?v=CDdvReNKKuk

To download mp4 video

> yt-dlp -f bestvideo[ext=mp4]+bestaudio[ext=m4a]/mp4 [youtube link]

e.g. 

> yt-dlp -f bestvideo[ext=mp4]+bestaudio[ext=m4a]/mp4 https://www.youtube.com/watch?v=CDdvReNKKuk

To set alias, read below.

# Alias

# Setup alias

Edit file ~/.bashrc

> nano ~/.bashrc

For example, add the following content at the end:

```
alias update='sudo apt update && sudo apt dist-upgrade && updatedb && youtube-dl -U'
alias mp3='yt-dlp -x --audio-format mp3'
alias mp4='yt-dlp -f bestvideo[ext=mp4]+bestaudio[ext=m4a]/mp4'
alias chrome='google-chrome-stable &>/dev/null & disown'
alias firefox='/home/eliranwong/.Applications/firefox/firefox &>/dev/null & disown'
alias win11='lxc console win11 --type=vga & disown'
alias annotator='com.github.phase1geo.annotator & disown'
```

# Free Up Space

> sudo apt -s clean

> sudo apt --purge autoremove

> journalctl --disk-usage<br>
> sudo journalctl --vacuum-time=3d

> sudo apt install stacer<br>
> stacer

# Copy Shortcuts on Desktop

Copy favourite shortcuts files

from:

> /usr/share/applications/

> /var/lib/snapd/desktop/applications/

to:

> ~/Desktop/

Right-click each shortcut file, select "Allow Launching"

# User Configurations

> ls ~/.config

# Assign Default Applications

Manually edit '/usr/share/applications/mimeapps.list' or '.config/mimeapps.list'

```
[Default Applications]
inode/directory=pcmanfm-qt.desktop
application/pdf=wps-office-pdf.desktop
```

Alternatively, use xdg-mime command, e.g.:

> xdg-mime default pcmanfm-qt.desktop inode/directory

> xdg-mime default wps-office-pdf.desktop application/pdf

> cat .config/mimeapps.list

# apt-repository

/etc/apt/sources.list.d

# Change Desktop to a Solid Color

Run in terminal:

> gsettings set org.gnome.desktop.background color-shading-type 'solid'
   
> gsettings set org.gnome.desktop.background picture-uri none
             
> gsettings set org.gnome.desktop.background picture-uri-dark none
           
> gsettings set org.gnome.desktop.background primary-color '#0D1117'

Alternately, use run gui app 'dconf-editor'

> sudo apt install dconf-editor

![background1](https://github.com/eliranwong/ubuntu_on_surface_go/assets/25262722/fb289c1b-8ae4-403d-882f-a7694a8ca5d9)

![background2](https://github.com/eliranwong/ubuntu_on_surface_go/assets/25262722/ffe5e59d-ba87-473c-8ff6-0bb6ffa24053)

# Replace PulseAudio with PipeWire on Ubuntu 22.04

The fix for PulseAudio, mentioned above, is not a satisfactory one.  Therefore, we find it better to replace PulseAudio with Pipewire on Ubuntu 22.04.

Check current status:

> systemctl --user status pipewire pipewire-session-manager

Set up:

> sudo add-apt-repository ppa:pipewire-debian/pipewire-upstream

> sudo add-apt-repository ppa:pipewire-debian/wireplumber-upstream

> sudo apt update && sudo apt dist-upgrade

> systemctl --user daemon-reload

> sudo apt install pipewire-audio-client-libraries libspa-0.2-bluetooth libspa-0.2-jack

> sudo apt install wireplumber pipewire-media-session-

NOTE: there’s a ‘-‘ in the end of the command indicates to remove the package. The command will also install the required pipewire-pulse automatically.

> systemctl --user --now enable wireplumber.service

Configure ALSA:

> sudo cp /usr/share/doc/pipewire/examples/alsa.conf.d/99-pipewire-default.conf /etc/alsa/conf.d/

Configure Jack:

> sudo cp /usr/share/doc/pipewire/examples/ld.so.conf.d/pipewire-jack-*.conf /etc/ld.so.conf.d/

> sudo ldconfig

Configure Bluetooth:

> sudo apt remove pulseaudio-module-bluetooth

Reboot to make all changes effective

> reboot

To verify, run:

> pactl info

Source: 

https://ubuntuhandbook.org/index.php/2022/04/pipewire-replace-pulseaudio-ubuntu-2204/

https://www.how2shout.com/linux/enable-pipewire-for-audio-and-bluetooth-in-ubuntu-22-04-or-20-04/

# Windows 11

https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/tree/main/ubuntu_desktop/win11_lxc_incus.md

# Set up Remote Desktop Server

https://github.com/eliranwong/ubuntu_on_surface_go/blob/main/setup_remote_destkop_on_ubuntu.md

# HP Printer Drivers

First, install libcanberra-gtk-module

> sudo apt install libcanberra-gtk-module

Then, download hplip software and install printer driver:

https://developers.hp.com/hp-linux-imaging-and-printing/

Download: https://developers.hp.com/hp-linux-imaging-and-printing/gethplip

Installation instructions: https://developers.hp.com/hp-linux-imaging-and-printing/install/install/index

Run 'hp-setup' after restart:

> hp-setup

Remarks: Enter IP address manually if printer is not found.

# Tutorials

https://ubuntu.com/tutorials

https://ubuntuhandbook.org/

# Useful Command Line Tools

https://github.com/eliranwong/ChromeOSLinux/blob/main/cli/common.md

# More Linux softwares

https://github.com/luong-komorebi/Awesome-Linux-Software