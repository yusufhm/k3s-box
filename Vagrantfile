# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004"
  config.vm.network "public_network", bridge: [
      "wlp0s20f3",
      "enp0s31f6",
  ]
  config.vm.hostname = "k3s.local"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = "2048"
    vb.name = "k3s"
  end

  config.vm.provision :ansible do |ansible|
    ansible.playbook = "vm-playbook.yml"
  end

end
