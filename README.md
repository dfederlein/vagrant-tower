Ansible Tower Demo-In-A-Box
===========================

This is a Tower demo environment in a box using Vagrant and VirtualBox.

Host Machine Requirements:

- Ansible (version 1.6 or later)
- Vagrant (version 1.4 or later)
- VirtualBox (version 4.3 or later)
- Git 
- An Internet connection to create the demo environment.
- The ability to run 5 VM's on the host you are using this on. 

Ansible install instructions here: http://docs.ansible.com/intro_installation.html

To install Vagrant and VirtualBox, please see http://docs.vagrantup.com/ and https://www.virtualbox.org/wiki/Downloads

**Note:** The demo requires an internet connection on your host machine both to set itself up and to deploy the included lamp_haproxy playbook. 

Demo Instructions
-----------------

This demo kit requires either a CentOS 6 or RHEL 6 vagrant box. You will need to add the box name and the url to the vagrant file. You can build your own vagrant box as long as that image is built according to the vagrant instructions and it includes the VirtualBox additions. Using an appropriate vagrant box from http://www.vagrantbox.es/ should also work. Please note:  Many of the vagrant boxes located at vagrantbox.es or minimal images on Amazon AWS do not have the requisite python packages installed.  For this reason, I have included the raw commands to install them in the common role.

Currently, I am testing against this vagrantbox: centos65-x86_64-20131205 URL: https://github.com/2creatives/vagrant-centos/releases/download/v6.5.1/centos65-x86_64-20131205.box

Preparing The Demo
------------------

### Cloning ###

To clone this demo to your local machine, execute the following command:
```
git clone https://github.com/dfederlein/vagrant-tower.git
```

### Networking ###

When preparing a demo you need to make a decision if you would like to make the systems availible to your public network or restrict them to the private subnet. Out of the box the demo is configured to run in a public/bridged network (the same as your host's LAN) and you will just want to make sure that your Ansible hosts file is configured properly. If you would like to NAT the hosts on a VirtualBox private network in addition to changing the Ansible hosts file you will need to update the provided Vagrantfile to reflect the proper IP information.

#### Ansible Hosts File ####

The demo provides an Ansible hosts file under roles/tower/files/hosts which should look like the below snippet. If you need to change the IPs for any reason you must edit this file so that the demo will function properly.

```
[Tower]
tower ansible_ssh_host=192.168.250.10

[webservers]
vm1 ansible_ssh_host=192.168.250.11
vm2 ansible_ssh_host=192.168.250.12

[dbservers]
vm3 ansible_ssh_host=192.168.250.13

[lbservers]
vm4 ansible_ssh_host=192.168.250.14

[monitoring]
vm5 ansible_ssh_host=192.168.250.15
```

#### Vagrantfile ####

The Vagrantfile allows us to make the networking configuration changes that will be used to create the demo VMs. If you are going to continue using private networking there are no changes required unless you need to change the IPs. If you are going to use a public network use the example below for each server (tower, vm1, vm2, web3, vm3, vm5). Remember that it is important that the provided Vagrantfile must be consistant with the Ansible hosts file.

Public (bridged NIC) IP:
```
  config.vm.define "tower" do |tower|
    tower.vm.network :public_network, ip: "XXX.XXX.XXX.XXX"
  end
```
Private (NAT'ed NIC) IP:
```
  config.vm.define "tower" do |tower|
    tower.vm.network :private_network, ip: "XXX.XXX.XXX.XXX"
  end
```
If you have any problems you should consult the Vagrant documentation.

#### Instantiation ####

Execute the command below to create and configure the VMs for the demo.
```
vagrant up
```

Running the Demo
----------------

Now you can log into tower at https://192.168.251.10 or the IP you have specified.  Further information about tower can be found here: http://www.ansible.com/tower  (User guide can be downloaded from that webpage.)

The admin user to log in as is:

User: admin
Password: password

The standard user to log in as is:

User: demo1
Password: Password1!

Once you have logged in Organization, Users, Teams, Inventories and Credentials won't be prepopulated.  You will need to add these things.  You will also need to add a project based on the SCM url below:

https://github.com/dfederlein/ansible-examples.git

Alternatively, you could write your own playbooks and place them locally in /var/lib/awx/projects or in your own public/private SCM repo.

If you changed the ip networking of the Vagrant boxes in the playbook as described above, you will need to reimport the inventory with your modified inventory file.  To re-import the host inventory from CLI to tower run the following commands:

```
vagrant ssh tower
sudo su -
awx-manage inventory_import --source=/etc/ansible/hosts --inventory-name=Vagrant --overwrite
```

Note: This file is placed by the tower role, then imported via the same playbook role task list.  If you change the file there before you run this demo, this will be automatic.  The above command is only needed if you change host IP address or host variable information within the /etc/ansible/hosts file and wish to re-import that to Tower.


To use the ansible CLI on the tower host:
```
vagrant ssh tower
```

Located in /home/vagrant/playbooks are playbooks taken from https://github.com/ansible/ansible-examples.  This demo is designed to run these playbooks (though the included playbooks are modified somewhat from the canonical ansible repo.)  From the ansible CLI you can modify then execute these playbooks as you see fit to learn more about ansible CLI.

Additionally, playbooks from the ansible-examples repo ( https://github.com/ansible/ansible-examples ) have been added to /var/lib/awx/projects/ so you can use them when creating projects and job templates in Tower. 

(NOTE: Included playbooks were adapted to work with this vagrant demo. You can view them at my fork of the originals: https://github.com/dfederlein/ansible-examples )

NOTE: In order to run these playbooks, a variable called uservar must be set.  This is to allow for vagrant boxes where the remote ssh user is vagrant, as well as amazon AMI/Rackspace images using another ssh user.  See below for CLI use.

For example, you can deploy lamp_haproxy as a stack of 5 machines (1 load balancer, 2 web servers, 1 DB server, 1 nagios server):
```
cd /home/vagrant/playbooks/lamp_haproxy/
ansible-playbook site.yml -e "uservar=vagrant"
```

Further material to get you started in ansible Tower can be found in this PDF: http://releases.ansible.com/ansible-tower/docs/tower_user_guide-latest.pdf 

You can also gain a wealth of knowledge about Ansible CLI at http://docs.ansible.com

**TROUBLESHOOTING**

- What's this warning for mpx_powm_sec? This is a warning for out of date native centos packages. two perl lines are included in the main.yml of the tower role to address this.  Note this bug report: https://github.com/ansible/ansible/issues/6941 (Will be fixed in future versions of this playbook demo)
- if for some reason the initial 'vagrant up' command fails, make sure you 'vagrant destroy' all machines before re-running the playbook or you may end up with missed plays.
- if you have changed the IP addresses in the Vagrantfile from above, you may need to adjust group variables in the example lamp_haproxy playbook to match your changes.  For example, in group_vars/lbservers you may need to change iface: eth1 to eth0.  The same would be true for group_vars/webservers.
- You will also need to change the file roles/tower/files/hosts to match IP addresses if you have changed the Vagrantfile provided to your own subnet.
- Using the static IPs above with VirtualBox means each machine will have two interfaces on two subnets.  One will be the default NAT'ed subnet for VirtualBox, the other the subnet you've specified.  This is true whether using private or public settings.  Adjust wanted communication paths in your playbooks accordingly, all subnets are functional on these hosts.

**TO-DO**

- Agnosticity between CentOS and Ubuntu LTS
