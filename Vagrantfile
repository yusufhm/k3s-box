# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004"
  config.vm.hostname = "k3s.localvm"

  # Feel free to change the IP in Vagrantfile.local.
  config.vm.network "private_network", ip: "192.168.31.4"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = "2048"
    vb.name = "k3s"
  end

  config.vm.provision :ansible do |ansible|
    ansible.playbook = "localvm.yml"
  end

end

# Load local config.
local_vagrantfile = 'Vagrantfile.local'
load local_vagrantfile if File.exist?(local_vagrantfile)
