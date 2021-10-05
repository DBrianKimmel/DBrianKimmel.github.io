# First Control Node

First we will pick out the computer we will use as the first control node.
This node will have several other functions:
- ansible installer for the cluster.
- central log for the cluster.

``` bash
sudo apt install arp-scan
```

## Ansible

### Install Ansible

```bash
sudo apt install ansible
```

Next, we need to create a file /etc/ansible/hosts (or edit it), and add our hosts. In essence, here we define hosts and groups of hosts that Ansible will try to manage.

# Edit file /etc/ansible/hosts

On pi-12-pp edit a hosts file for ansible.

``` bash
sudo nano /etc/ansible/hosts
```

```ini
[control]
control01  ansible_connection=local var_hostname=pi-12-pp
control02  ansible_connection=ssh   var_hostname=pi-17-pp
control03  ansible_connection=ssh   var_hostname=pi-18-pp

[workers]
cube01  ansible_connection=ssh  var_hostname=pi-15-pp
cube02  ansible_connection=ssh  var_hostname=pi-16-pp

[cube:children]
control
workers
```

# Master 1

In our case: pi-12-pp

This is our primary node, one of 3 control nodes.

We are going to install the K3s version.
Use the following command to download and initialize K3s’ master node.
We pasted the --server into the command to tell it that we will be adding additional master nodes:

If k3s has been installed kill it and remove it:
``` bash
sudo k3s-killall.sh
sudo k3s-uninstall.sh
```

``` bash
curl -sfL https://get.k3s.io | \
    K3S_TOKEN="some_random_password" \
    K3S_KUBECONFIG_MODE="644" \
    sh -s - server --cluster-init --disable servicelb
```
