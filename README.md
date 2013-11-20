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

First, understand that this demo kit requires either a CentOS 6 or Ubuntu LTS vagrant box.  Add box name and url accordingly to the vagrant file.

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