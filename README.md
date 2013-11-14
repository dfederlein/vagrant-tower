AWX Demo-In-A-Box
=================

This is a AWX demo environment in a box using Vagrant and VirtualBox.

Requirements:

- Ansible
- Vagrant
- VirtualBox
- An Internet connection to create the demo environment.

**Note:** The demo instance itself, once created, should be functional without
an Internet connection. 

To use:

First, edit the Vagrantfile and comment out the box type you don't want to use.  Included is a minimal CentOS and a minimal Ubuntu 12.04 LTS or you can specify your own.

Next, set the networking configuration options you want in the Vagrantfile.  For example:

Public (bridged NIC) IP:
```
  config.vm.define "awx" do |awx|
    awx.vm.network :public_network, ip: "XXX.XXX.XXX.XXX"
  end
```
Private (NAT'ed NIC) IP:
```
  config.vm.define "awx" do |awx|
    awx.vm.network :private_network, ip: "XXX.XXX.XXX.XXX"
  end
```
Or configure as needed per Vagrant docs.

Then:
```
vagrant up
```

Then log into AWX at your specified IP.