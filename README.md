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

## Get started

Install python libraries
```sh
pip install boto3 openshift
```

Install Ansible collections
```sh
ansible-galaxy collection install -r requirements.yml
```

After ensuring all requirements are installed and creating `Vagrantfile.local` as above, run
```sh
vagrant up
```
That should automatically set up k3s in the VM and install the kube dashboard.

If you use direnv, copy `.envrc.example` to `.envrc` and update values if required. Then run `direnv allow` from inside the directory to ensure the `KUBECONFIG` variable is set to the `kubeconfig` file in the project directory. Then you can just use `kubectl` as usual
```sh
kubectl cluster-info
```

If not you'll need to manually specify the kubeconfig when running kubectl, for example
```sh
kubectl --kubeconfig=.kubeconfig/k3s.yaml cluster-info
```
or set the `KUBECONFIG` variable first
```sh
export KUBECONFIG=.kubeconfig/k3s.yaml
kubectl cluster-info
```

To access the dashboard, first fetch the token for the admin user
```sh
kubectl -n kubernetes-dashboard describe secret admin-user-token | grep '^token'
```

The dashboard is accessible at https://dashboard.k3svm/. See next steps on how to configure hosts for `.k3svm` to work.

## Next steps

Apps are installed from `localvm-apps/app.*.yml`. The `nginx` app, for instance, gets installed, complete with a hostname of `nginx.k3svm`; in order to access it from the host, that hostname will need to be added to `/etc/hosts`, pointing to the `eth1` IP address of the VM.

Alternatively you could set up wildcard DNS using dnsmasq. The following steps assumes Ubuntu 20.04 (taken from https://askubuntu.com/questions/1029882/how-can-i-set-up-local-wildcard-127-0-0-1-domain-resolution-on-18-04-20-04):

### Enable dnsmasq in NetworkManager
Edit the file /etc/NetworkManager/NetworkManager.conf, and add the line dns=dnsmasq to the [main] section, it will look like this :

```
[main]
plugins=ifupdown,keyfile
dns=dnsmasq

[ifupdown]
managed=false

[device]
wifi.scan-rand-mac-address=no
```

### Let NetworkManager manage /etc/resolv.conf
```sh
sudo rm /etc/resolv.conf ; sudo ln -s /var/run/NetworkManager/resolv.conf /etc/resolv.conf
```

### Configure `.k3svm`
```sh
echo 'address=/.k3svm/192.168.31.4' | sudo tee /etc/NetworkManager/dnsmasq.d/k3svm.conf
```

### Reload NetworkManager and test
```sh
sudo systemctl reload NetworkManager
dig nginx.k3svm something-else.k3svm
```
