# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004"
  config.vm.hostname = "k3s.localvm"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = "2048"
    vb.name = "k3s"
  end

  config.vm.provision :ansible do |ansible|
    ansible.playbook = "vm-playbook.yml"
  end

end

# Load local config.
load 'Vagrantfile.local'
