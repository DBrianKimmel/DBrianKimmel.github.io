# Additional Control Nodes

By using the following command on both nodes, we will add these to the cluster as master nodes containing the etcd database:

``` bash
curl -sfL https://get.k3s.io | \
    K3S_TOKEN="some_random_password" \
    K3S_KUBECONFIG_MODE="644" \
    sh -s - server --server https://172.16.2.12:6443 --no-deploy servicelb
```

You can do that with Ansible as well:

``` bash
ansible pi-17-pp,pi-18-pp -b -m shell -a "curl -sfL https://get.k3s.io | \
    K3S_TOKEN="some_random_password" \
    K3S_KUBECONFIG_MODE="644" \
    sh -s - server --server https://172.16.2.12:6443 --no-deploy servicelb"
```

For the --server parameter, we are using the IP of our primary master node. This will create a control plane for our cluster.

The control plane should be like this:

``` bash
root@control01:/home/ubuntu# kubectl get nodes
NAME        STATUS   ROLES         AGE    VERSION
control01   Ready    etcd,master   5d3h   v1.19.4+k3s1
control02   Ready    etcd,master   5d3h   v1.19.4+k3s1
control03   Ready    etcd,master   5d3h   v1.19.4+k3s1
```
