# K3s Cluster Guide
First, install Ubuntu server (in my case, clone a cloud-init template). 
## Keepalived Load Balancer
1. Update, upgrade, and install keepalived
```
sudo apt update
sudo apt upgrade
sudo apt install -y keepalived
```
2. Create a Keepalived config file:
```
sudo nano /etc/keepalived/keepalived.conf
```
3. For the first node (highest priority):
```
vrrp_instance K3S_VIP {
    state MASTER
    interface eth0  # Change this to your network interface (use `ip a` to check)
    virtual_router_id 51
    priority 150  # Higher priority for the primary node
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass mysupersecretpassword # This should be the same on all nodes
    }
    virtual_ipaddress {
       10.0.0.100/24  # Your desired Virtual IP
    }
}

```
4. On nodes two and three:
```
vrrp_instance K3S_VIP {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 100  # Lower priority
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass mysupersecretpassword
    }
    virtual_ipaddress {
        10.0.0.100/24
    }
}
```
5. Restart and enable keepalived:
```
sudo systemctl restart keepalived
sudo systemctl enable keepalived
```
6. Verify VIP assignment:
```
ip a | grep 10.0.0.100
```
## K3s Installation
1. Let's start by installing K3s on the master node:
```
curl -sfL https://get.k3s.io | K3S_TOKEN="supersecret" sh -s - server \
    --cluster-init \
    --tls-san=10.0.0.100

```
I believe ```--cluster-init``` will initialize the embedded etcd database. You can also use PostgreSQL or MySQL.

2. We need at least three control plane nodes. We can install K3s and join the cluster with the following command:
```
curl -sfL https://get.k3s.io | K3S_TOKEN="supersecret" sh -s - server \
    --server https://10.0.0.100:6443
```
3. After everything finishes, use ```sudo kubectl get nodes``` to check if everything is working.
```
chris@k3s-1:~$ sudo kubectl get nodes
NAME    STATUS   ROLES                       AGE   VERSION
k3s-1   Ready    control-plane,etcd,master   19m   v1.31.5+k3s1
k3s-2   Ready    control-plane,etcd,master   14m   v1.31.5+k3s1
k3s-3   Ready    control-plane,etcd,master   14m   v1.31.5+k3s1
```
