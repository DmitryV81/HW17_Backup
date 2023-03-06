# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "site.yaml"
    ansible.become = "true"
  end

  config.vm.provider "virtualbox" do |v|
    v.memory = 2096
    v.cpus = 1
  end

  config.vm.define "backup" do |backup|
    backup.vm.network "private_network", ip: "192.168.11.160", virtualbox__intnet: "net1"
    backup.vm.hostname = "backup"
    backup.vm.provider "virtualbox" do |vb|
      unless File.exist?('./storage/backup.vdi')
            vb.customize ['createhd', '--filename', './storage/backup.vdi', '--variant', 'Fixed', '--size', 2176]
            needsController = true
      end
      vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata"]
      vb.customize ['storageattach', :id, '--storagectl', 'SATA', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', './storage/backup.vdi']
    end
  end

  config.vm.define "client" do |client|
    client.vm.network "private_network", ip: "192.168.11.150", virtualbox__intnet: "net1"
    client.vm.hostname = "client"
  end

 
end
