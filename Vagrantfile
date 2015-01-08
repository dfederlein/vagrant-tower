# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "matyunin/centos7"

  config.vm.define "tower", primary: true do |tower|
      config.vm.provider :virtualbox do |vb, override|
        vb.customize ["modifyvm", :id, "--memory", "2048"]
      end
    tower.vm.network :public_network, ip: "192.168.250.10"
  end

  config.vm.define "vm1" do |vm1|
    vm1.vm.network :public_network, ip: "192.168.250.11"
  end  

  config.vm.define "vm2" do |vm2|
    vm2.vm.network :public_network, ip: "192.168.250.12"
  end  

  config.vm.define "vm3" do |vm3|
    vm3.vm.network :public_network, ip: "192.168.250.13"
  end 

  config.vm.define "vm4" do |vm4|
    vm4.vm.network :public_network, ip: "192.168.250.14"
  end 

  config.vm.define "vm5" do |vm5|
    vm5.vm.network :public_network, ip: "192.168.250.15"
  end
 
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "demo-site.yml"
    ansible.verbose = "v"
    ansible.sudo = true
    ansible.sudo_user = "root"
    ansible.host_key_checking = false
  end
end
