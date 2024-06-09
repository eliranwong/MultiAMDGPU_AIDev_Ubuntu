# synaptic

To install synaptic:

> sudo apt install synaptic

To run synaptic without root privileges:

> synaptic-pkexec

Enable root privileges:

First give your linux container access to the host display.  Please note that chrome OS is the host.]<br>
> xhost +si:localuser:root

If you do not want to enter the line above each time you run synaptic:

> echo "xhost +si:localuser:root" >> ~/.profile

Then, you can run synaptic with sudo command:

> sudo synaptic
