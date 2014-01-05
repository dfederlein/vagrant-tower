AWX Demo-In-A-Box
=================

This is a AWX demo environment in a box using Vagrant and VirtualBox.

Host Machine Requirements:

- Ansible (version 1.3.X or later)
- Vagrant (version 1.3.3 or later)
- VirtualBox (version 4.3 or later with Oracle VM VirtualBox Extension Pack installed.)
- Git 
- An Internet connection to create the demo environment.
- The ability to run 5 VM's on the host you are using this on. (included CentOS vagrantbox uses 10g root filesystem and 1g ram max, is thin provisioned.)

Ansible install instructions here: http://www.ansibleworks.com/docs/intro_installation.html

To install Vagrant and VirtualBox, please see http://docs.vagrantup.com/ and https://www.virtualbox.org/wiki/Downloads

**Note:** The demo requires an internet connection on your host machine both to set itself up and to deploy the included lamp_haproxy playbook. 

Demo Instructions
-----------------

This demo kit requires either a CentOS 6 or RHEL 6 vagrant box. It is suggested to use the included vagrant box but you are able to create your own. To use your own you will need to add the box name and the url to the vagrant file. You can build your own vagrant box given that the image is built to the according to the vagrant instructions making sure that it contains the VirtualBox additions. Using an appropriate vagrant box from http://www.vagrantbox.es/ should also work. 

Preparing The Demo
------------------

### Cloning ###

To clone this demo to your local machine, execute the following command:
```
git clone https://github.com/dfederlein/vagrant-awx.git
```

### Networking ###

When preparing a demo you need to make a decision if you would like to make the systems availible to your public network or restrict them to the private subnet. Out of the box the demo is configured to run in a private network and you will just want to make sure that your Ansible hosts file is configured properly. If you would like to share the systems on a public network in addition to changing the Ansible hosts file you will need to update the provided Vagrantfile to reflect the proper IP information. If there is a reason that you need to change the IPs in a private network configuration you will need to modify the Ansible hosts file as well as the Vagrant file.

#### Ansible Hosts File ####

The demo provides an Ansible hosts file under roles/awx/files/hosts which should look like the below snippet. If you need to change the IPs for any reason you must edit this file so that the demo will function properly.

```
[awx]
awx ansible_ssh_host=192.168.250.10

[webservers]
web1 ansible_ssh_host=192.168.250.11
web2 ansible_ssh_host=192.168.250.12

[dbservers]
db1 ansible_ssh_host=192.168.250.13

[lbservers]
web3 ansible_ssh_host=192.168.250.14

[monitoring]
mn1 ansible_ssh_host=192.168.250.15
```

#### Vagrantfile ####

The Vagrantfile allows us to make the networking configuration changes that will be used to create the demo VMs. If you are going to continue using private networking there are no changes required unless you need to change the IPs. If you are going to use a public network use the example below for each server (awx, web1, web2, web3, db1, mn1). Remember that it is important that the provided Vagrantfile must be consistant with the Ansible hosts file.

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
If you have any problems you should consult the Vagrant documentation.

#### Instantiation ####

Execute the command below to create and configure the VMs for the demo.
```
vagrant up
```

Running the Demo
----------------

Now you can log into AWX at https://192.168.250.10 or the IP you have specified.  Further information about AWX can be found here: http://www.ansibleworks.com/releases/awx/docs/awx_user_guide-latest.pdf

The user to log in as is:

User: admin
Password: password

Once you have logged in, you can create your Organization, Users, Teams, Inventories and Credentials.  When creating Projects, you will be able to select the example lamp_haproxy playbook project folder (initial deploy of demo places that for you.)  To import the provided host inventory from CLI to AWX, log into the awx host, su to root and run the following commands:

```
vagrant ssh awx
sudo su -
awx-manage inventory_import --source=/etc/ansible/hosts --inventory-name=(your created inventory name)
```

To use the ansible CLI on the awx host:
```
vagrant ssh awx
```

Located in /home/vagrant/projects is a playbook taken from https://github.com/ansible/ansible-examples called "lamp_haproxy."  This demo is designed to run this playbook (though the included version is modified somewhat.)  From the ansible CLI you can modify then execute this playbook as you see fit to learn more about ansible CLI.
```
cd /home/vagrant/projects/lamp_haproxy/
ansible-playbook site.yml
```

Further material to get you started in ansible CLI can be found here:  http://www.ansibleworks.com/docs/

**TROUBLESHOOTING**

- if for some reason the initial 'vagrant up' command fails, make sure you 'vagrant destroy' all machines before re-running the playbook or you may end up with missed plays.
- if you have changed the IP addresses in the Vagrantfile from above, you may need to adjust group variables in the example lamp_haproxy playbook to match your changes.  For example, in group_vars/lbservers you may need to change iface: eth1 to eth0.  The same would be true for group_vars/webservers.
- You will also need to change the file roles/awx/files/hosts to match IP addresses if you have changed the Vagrantfile provided to your own subnet.
- Using the static IPs above with VirtualBox means each machine will have two interfaces on two subnets.  One will be the default NAT'ed subnet for VirtualBox, the other the subnet you've specified.  This is true whether using private or public settings.  Adjust wanted communication paths in your playbooks accordingly, all subnets are functional on these hosts.

**TO-DO**

- Agnosticity between CentOS and Ubuntu LTS
- assume non-friendly vagrant box (ie: not ours) and setup box accordingly in initial launch plays.
- Pre-populate AWX with org/team/user/credential/inventory/playbook information to match CLI env inventory and playbook.
- Add AWS scripts to demostrate hybrid launches and orchestration
