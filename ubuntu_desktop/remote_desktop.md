# Set up Remote Destkop Service on Ubuntu

PART 1 - Install and Enable Remote Desktop Package

PART 2 - Enable Sharing in Gnome Desktop Settings

PART 3 - Export environment variables

PART 4 - Connect from a Remote Desktop Client

# 1 Install and Enable Remote Desktop Package

> sudo apt update && sudo apt dist-upgrade

Install the XRDP package by running the following command in your terminal:

> sudo apt install xrdp

Once the installation is complete, start the XRDP service using the following command:

> sudo systemctl start xrdp

To allow XRDP to start automatically after each system reboot, run the following command:

> sudo systemctl enable xrdp

Add xrdp to the ssl-cert group, to enable it to use the /etc/ssl/private/ssl-cert-snakeoil.key file:

> sudo adduser xrdp ssl-cert

Remarks: /etc/ssl/private/ssl-cert-snakeoil.key is used in cases when no other SSL certificate is installed or configured, but encrypted communication is still enabled and desired. It still encrypts traffic, however since it lacks a root authority signature, it is still vulnerable to most man-in-the-middle attacks. That is why it’s called snakeoil.

By default, XRDP listens on port 3389. Make sure this port is open in your firewall to allow incoming connections. You can use the following command to open the port:

> sudo ufw allow 3389/tcp

To check Status, run:

> sudo systemctl status xrdp

# 2 Enable Sharing in Gnome Desktop Settings

1. Launch Settings
2. Open "Sharing" session
3. Select "Remote Desktop"
4. Enable "Remote Desktop"
5. Enable "Remote Control"

# 3 Export environment variables

Run in Terminal:

> sudo nano /etc/xrdp/startwm.sh

Place the following two lines at the very beginning of the file and restart Ubuntu:

> export GNOME_SHELL_SESSION_MODE=ubuntu<br>
> export XDG_CURRENT_DESKTOP=ubuntu:GNOME

# 4 Connect from a Remote Desktop Client

Check Ubuntu device ip address, in Ubuntu Terminal app, run:

> ip addr

Set up a remote desktop client

1. For an example, on macOS, install "Microsoft Remote Destkop" app via App Store.
2. Launch "Microsoft Remote Destkop"
3. Click the "+" sign to add a pc
4. Enter the ip address in "PC name" field

Connect from client to server:

1. Log out from Ubuntu device FIRST!
2. Double-click the pc thumbnail on "Microsoft Remote Desktop" app
3. Enter username and password
