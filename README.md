# k3s in a box using Vagrant and Ansible

Quickly set up and test [k3s](https://k3s.io/) locally.

## Requirements

  - [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
  - [Vagrant](https://www.vagrantup.com/downloads)
  - [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
  - [kubernetes.core Ansible collection](https://docs.ansible.com/ansible/latest/collections/kubernetes/core/k8s_module.html)
  - [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) (optional to talk to the cluster from the host)
  - [direnv](https://direnv.net/docs/installation.html) (optional to automatically tell kubectl which config file to use to talk to the cluster)

### Bridged network for the Vagrant box

By default vagrant will not create a bridged network, however we do require one while setting up the VM. So create a `Vagrantfile.local` and put the following contents in it:

```ruby
Vagrant.configure("2") do |config|
  config.vm.network "public_network", bridge: [
    "wlp0s20f3",
    "enp0s31f6",
  ]
end
```

Make sure to replace `wlp0s20f3` and `enp0s31f6` with your interfaces. Get a list of your interfaces (Linux) with
```sh
ip -brief addr show|grep UP
```

## Get started

After ensuring all requirements are installed and creating `Vagrantfile.local` as above, run
```sh
vagrant up
```
