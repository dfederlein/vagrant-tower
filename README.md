AWX Demo-In-A-Box
=================

This is a AWX demo environment in a box using Vagrant and VirtualBox.

Requirements:

- Ansible
- Vagrant
- VirtualBox
- An Internet connection to create the demo environment.
- The ability to run 6 VM's on the host you are using this on.

**Note:** The demo instance itself, once created, should be functional without
an Internet connection. 

Hosts:

Once up, your demo boxes will be as follows:
```
[awx]
awx ansible_ssh_host=192.168.250.10

[webservers]
web1 ansible_ssh_host=192.168.250.11
web2 ansible_ssh_host=192.168.250.12

[dbservers]
db1 ansible_ssh_host=192.168.250.13

[lbservers]
lb1 ansible_ssh_host=192.168.250.14

[monitoring]
nagios ansible_ssh_host=192.168.250.15
```
Modify these IP addresses to suit your needs in the Vagrantfile and in roles/awx/files/ansible.cfg (See below about nat vs. bridges IPs)

To use:

First, understand that this demo kit requires either a CentOS 6 or Ubuntu LTS vagrant box.  Add box name and url accordingly to the vagrant file. Currently, this has a Ubuntu LTS and Centos6.4 box I created coming from my S3 bucket, these may not exist in the future, plese use your own.  Minimal installs of either OS is fine, given the image is built to the vagrant instructions, or you can pull from http://www.vagrantbox.es/ 

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

Then log into AWX at https://192.168.250.10 or the IP you have specified, or to use ansible CLI:
```
vagrant ssh awx
```

Located in /home/vagrant/ansible is a playbook taken from https://github.com/ansible/ansible-examples called "lamp_haproxy."  This demo is designed to run this playbook.  from the ansible CLI, you can modify then execute this playbook as you see fit to learn more about ansible CLI.  