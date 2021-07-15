# k3s in a box using Vagrant and Ansible

Quickly set up and test [k3s](https://k3s.io/) locally.

## Requirements

  - [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
  - [Vagrant](https://www.vagrantup.com/downloads)
  - [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
  - `openshift` python library (required by the `kubernetes.core` Ansible Collection)
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

Install the `openshift` python library
```sh
pip install openshift
```

Install the `kubernetes.core` Ansible Collection
```sh
ansible-galaxy collection install kubernetes.core
```

After ensuring all requirements are installed and creating `Vagrantfile.local` as above, run
```sh
vagrant up
```
That should automatically set up k3s in the VM and install the kube dashboard.

If you use direnv, you can run `direnv allow` from inside the directory to ensure the `KUBECONFIG` variable is set to the `kubeconfig` file in the project directory. Then you can just use `kubectl` as usual
```sh
kubectl cluster-info
```

If not you'll need to manually specify the kubeconfig when running kubectl, for example
```sh
kubectl --kubeconfig=.kubeconfig/k3s.yaml cluster-info
```

To access the dashboard, first fetch the token for the admin user
```sh
kubectl -n kubernetes-dashboard describe secret admin-user-token | grep '^token'
```

Then start a proxy
```sh
kubectl proxy
```

The dashboard is accessible at http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

## Next steps

Apps are installed from `k8-configs/app.*.yml`. The `nginx` app, for instance, gets installed, complete with a hostname of `nginx.localvm`; in order to access it from the host, that hostname will need to be added to `/etc/hosts`, pointing to the `eth1` IP address of the VM.
