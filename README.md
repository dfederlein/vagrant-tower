AWX Demo-In-A-Box
=================

This is a AWX demo environment in a box using Vagrant and VirtualBox.

Host Machine Requirements:

- Ansible (version 1.3.X or later)
- Vagrant (version 1.3.3 or later)
- VirtualBox (version 4.3 or later with Oracle VM VirtualBox Extension Pack installed.)
- Git 
- An Internet connection to create the demo environment.
- The ability to run 6 VM's on the host you are using this on. (included CentOS vagrantbox uses 10g root filesystem and 1g ram max, is thin provisioned.)

Ansible install instructions here: http://www.ansibleworks.com/docs/intro_installation.html

To install Vagrant and VirtualBox, please see http://docs.vagrantup.com/ and https://www.virtualbox.org/wiki/Downloads

**Note:** The demo requires an internet connection on your host machine both to set itself up and to deploy the included lamp_haproxy playbook. 

**INSTRUCTIONS:**

This demo kit requires either a CentOS 6 or RHEL 6 vagrant box.  To use your own, add box name and url accordingly to the vagrant file. Currently, this has a Centos6.4 box I created coming from my S3 bucket, these may not exist in the future.  You can build your own, given the image is built to the vagrant instructions found here: https://gist.github.com/luis-ca/1327607 , or you can pull from http://www.vagrantbox.es/ 

To clone this demo to your local machine, execute the following command:
```
git clone https://github.com/dfederlein/vagrant-awx.git
```

First, set the networking configuration options you want in the Vagrantfile.  If you only intend to test this on your personal desktop or laptop, leave "private_network" in place.  If you want to demonstrate this for others on your local LAN, use "public_network" and set your IP addresses according to your local LAN subnet.  For example:

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

Modify these IP addresses to suit your needs in the Vagrantfile and in roles/awx/files/ansible.cfg to match Vagrantfile entries.

Virtual Hosts:

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

**GETTING STARTED**

Now you can log into AWX at https://192.168.250.10 or the IP you have specified.  Further information about AWX can be found here: http://www.ansibleworks.com/releases/awx/docs/awx_user_guide-latest.pdf

To use the ansible CLI on the awx host:
```
vagrant ssh awx
```

Located in /home/vagrant/ansible is a playbook taken from https://github.com/ansible/ansible-examples called "lamp_haproxy."  This demo is designed to run this playbook.  From the ansible CLI you can modify then execute this playbook as you see fit to learn more about ansible CLI.
```
cd /home/vagrant/ansible/lamp_haproxy/
ansible-playbook site.yml
```

Further material to get you started in ansible CLI can be found here:  http://www.ansibleworks.com/docs/

**TO-DO**

- Agnosticity between CentOS and Ubuntu LTS
- Pre-populate AWX with org/team/user/credential/inventory/playbook information to match CLI env inventory and playbook.