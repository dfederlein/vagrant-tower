AWX Demo-In-A-Box
=================

This is a AWX demo environment in a box using Vagrant and VirtualBox.

Requirements:

- Ansible
- Vagrant
- VirtualBox
- A VirtualBox host-only networking configuration on 192.168.250.xx (or a bridged 
	network connection to that subnet, see the public vs. private set in the 
	Vagrant file.)
- An Internet connection to create the demo environment.

**Note:** The demo rig itself, once created, should be functional without
an Internet connection. This will be useful for tradeshows and difficult
customer environments which do not have reliable network connections.

To use:

```
vagrant up
```

Then log into AWX at https://192.168.250.10/

**Note:** We use a pre-generated SSH key pair to authenticate to the demo
boxes. Don't use those keys for anything secure.
