# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = ""
  config.vm.box_url = ''

  config.vm.define "awx" do |awx|
    awx.vm.network :public_network, ip: "192.168.250.10"
  end
 
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "demo-site.yml"
    ansible.verbose = "v"
    ansible.sudo = true
    ansible.sudo_user = "root"
    ansible.host_key_checking = "false"
  end
end
