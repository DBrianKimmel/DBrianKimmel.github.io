# First Control Node

First we will pick out the computer we will use as the first control node.
This node will have several other functions:
- ansible installer for the cluster.
- central log for the cluster.

```bash
sudo apt install arp-scan
```

## Ansible

### Install Ansible

```bash
sudo apt install ansible
```

Next, we need to create a file /etc/ansible/hosts (or edit it), and add our hosts. In essence, here we define hosts and groups of hosts that Ansible will try to manage.

# Edit file /etc/ansible/hosts

```ini
[control]
control01  ansible_connection=local var_hostname=control01
control02  ansible_connection=ssh   var_hostname=control02
control03  ansible_connection=ssh   var_hostname=control03

[workers]
cube01  ansible_connection=ssh  var_hostname=cube01
cube02  ansible_connection=ssh  var_hostname=cube02
cube03  ansible_connection=ssh  var_hostname=cube03
cube04  ansible_connection=ssh  var_hostname=cube04
cube05  ansible_connection=ssh  var_hostname=cube05
cube06  ansible_connection=ssh  var_hostname=cube06

[cube:children]
control
workers
```
