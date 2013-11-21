AWX Demo-In-A-Box
=================

This is a AWX demo environment in a box using Vagrant and VirtualBox.

Requirements:

- Ansible
- Vagrant
- VirtualBox
- An Internet connection to create the demo environment.
- The ability to run 6 VM's on the host you are using this on. (included CentOS vagrantbox uses 10g root filesystem and 1g ram max, is thin provisioned.)

To install the first three requirements, please see http://docs.vagrantup.com/

**Note:** The demo requires an internet connection on your host machine both to set itself up and to deploy the included lamp_haproxy playbook. 

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
Modify these IP addresses to suit your needs in the Vagrantfile and in roles/awx/files/ansible.cfg (See below about nat vs. bridged IPs)

**To use:**

First, understand that this demo kit requires either a CentOS 6 or RHEL 6 vagrant box.  Add box name and url accordingly to the vagrant file. Currently, this has a Centos6.4 box I created coming from my S3 bucket, these may not exist in the future, plese use your own.  Minimal installs of either OS is fine, given the image is built to the vagrant instructions, or you can pull from http://www.vagrantbox.es/ 

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

Located in /home/vagrant/ansible is a playbook taken from https://github.com/ansible/ansible-examples called "lamp_haproxy."  This demo is designed to run this playbook.  From the ansible CLI you can modify then execute this playbook as you see fit to learn more about ansible CLI.
```
cd /home/vagrant/ansible/lamp_haproxy/
ansible-playbook site.yml
```

**TO-DO**

1) Agnosticity between CentOS and Ubuntu LTS
2) pre-populate AWX with org/team/user/credential/inventory/playbook information to match CLI env inventory and playbook.