AWX Demo-In-A-Box
=================

This is a AWX demo environment in a box using Vagrant and VirtualBox.

Note:  For now, you must use a CentOS vagrant box (regardless of provider).  
		To-do: Create a Ubuntu LTS variable set/role dependency for use on either
				supported platform (RHEL/CentOS and Ubuntu LTS are only supported for AWX.)

Requirements:

- Ansible
- Vagrant
- VirtualBox
- A VirtualBox host-only networking configuration on 192.168.250.xx (or a bridged 
	network connection to that subnet, see the public vs. private set in the 
	Vagrant file.)
- An Internet connection to create the demo environment.

**Note:** The demo rig itself, once created, should be functional without
an Internet connection. 

To use:

```
vagrant up
```

Then log into AWX at https://192.168.250.10/