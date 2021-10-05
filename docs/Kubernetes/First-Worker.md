# First Worker Node

We need to join some workers now; in our case pi-15-pp and pi-16-pp.

On every worker node do the following:

``` bash
curl -sfL https://get.k3s.io | \
    K3S_URL="https://172.16.2.12:6443" \
    K3S_TOKEN="some_random_password" sh -
```

You can do that with ansible as well:

``` bash
ansible workers -b -m shell -a "curl -sfL https://get.k3s.io | \
    K3S_URL="https://172.16.2.12:6443" \
    K3S_TOKEN="some_random_password" sh -"
```

## Setting role/labels

We can tag our cluster nodes to give them labels.

Important

k3s by default allow pods to run on the control plane, which can be OK, but in production it would not. However, in our case, we want to use disks on control nodes for storage, and that does require pods to run on them from Longhorn. So, I'll be using labels to tell pods/deployment where to run.

Lets add this tag key:value: kubernetes.io/role=worker to worker nodes. This is more cosmetic, to have nice output from kubectl get nodes.

``` bash
kubectl label nodes pi-15-pp kubernetes.io/role=worker
kubectl label nodes pi-16-pp kubernetes.io/role=worker
```

Another label/tag. I will use this one to tell deployments to prefer nodes where node-type equals workers. The node-type is our chosen name for key, you can call it whatever.

``` bash
kubectl label nodes pi-15-pp node-type=worker
kubectl label nodes pi-16-pp node-type=worker
```

Whole Kubernetes cluster:

``` bash
kubectl get nodes
NAME        STATUS   ROLES         AGE    VERSION
pi-12-pp    Ready    etcd,master   5d3h   v1.19.4+k3s1
pi-17-pp    Ready    etcd,master   5d3h   v1.19.4+k3s1
pi-18-pp    Ready    etcd,master   5d3h   v1.19.4+k3s1
pi-15-pp    Ready    worker        5d3h   v1.19.4+k3s1
pi-16-pp    Ready    worker        5d3h   v1.19.4+k3s1
```

You can also use kubectl get nodes --show-labels to show all labels for nodes.

Lastly, add following into /etc/environment (this is so the Helm and other programs know where the Kubernetes config is.)

On every node:

``` bash
echo "KUBECONFIG=/etc/rancher/k3s/k3s.yaml" >> /etc/environment
```

Or use Ansible:

``` bash
ansible cube -b -m lineinfile -a "path='/etc/environment' line='KUBECONFIG=/etc/rancher/k3s/k3s.yaml'"
```
