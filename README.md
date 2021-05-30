# k8s-deployments
Group of k8s deployments with detailed steps

## Setup a Raspberry Pi Cluster

Follow the hardware setup [here](https://github.com/geerlingguy/raspberry-pi-dramble#setting-up-the-pis)

The following is my setup. The heatsink and a USB fan can make sure the CPU temp under 40 degree. 

 Cluster Back              |  Cluster Front
:-------------------------:|:-------------------------:
![Cluster Back](https://github.com/GuanyiLi-Craig/k8s-deployments/blob/main/img/Cluster_back.jpg)  |  ![Cluster Front](https://github.com/GuanyiLi-Craig/k8s-deployments/blob/main/img/Cluster_front.jpg)

## Install K3S 

### Setup Master Node
* Choose a node (raspberry pi) as a master node
* Install K3S by
```bash
curl -sfL https://get.k3s.io | sh -
```
* Check install status
```bash
sudo kubectl get nodes
```
* Get token and copy the string to somewhere else
```bash
sudo cat /var/lib/rancher/k3s/server/node-token
```
### Setup Worker Nodes
* suppose the master node's ip is 192.168.1.100 and token is \<TOKEN\>, the worker name is k3s-node-1
```bash
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.1.100:6443 K3S_TOKEN=<TOKEN> K3S_NODE_NAME=k3s-node-1 sh -
```
* Wait about 1-2 mins and check the worker node setup in master node by
```bash
sudo kubectl get node -o wide
```
* Follow the same steps to add more worker nodes with different worker names. 
* Nit: add role to worker node
```bash
sudo kubectl label node k3s-node-1 node-role.kubernetes.io/worker=worker
```
