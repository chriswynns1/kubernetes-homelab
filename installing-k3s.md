# How I set up my first K3S server:

## Server setup:
1. Install ubuntu server as normal
2. Configure port 1 on second nic to use routes in ```/etc/netplan/```:
```
routes:
  - to: 0.0.0.0/0
    via: 192.168.1.1/24
```
3. Configure ssh:
```
  sudo systemctl ssh enable
  sudo systemctl ssh start
  sudo nano /etc/ssh/sshd_config
```
4. Update, upgrade, and install k3s:
```
  sudo apt update
  sudo apt upgrade
  curl -sfL https://get.k3s.io | sh -
```
5. Check if installation was successful:
```
chris@node0:~$ sudo kubectl get nodes
NAME    STATUS   ROLES                  AGE   VERSION
node0   Ready    control-plane,master   16m   v1.31.4+k3s1
```
6. Check pods using ```sudo kubectl get pods -A```
```
chris@node0:~$ sudo kubectl get pods -A
[sudo] password for chris:
NAMESPACE     NAME                                      READY   STATUS      RESTARTS   AGE
kube-system   coredns-ccb96694c-qszkr                   1/1     Running     0          13m
kube-system   helm-install-traefik-crd-4jn8z            0/1     Completed   0          13m
kube-system   helm-install-traefik-slgqb                0/1     Completed   1          13m
kube-system   local-path-provisioner-5cf85fd84d-nbhdc   1/1     Running     0          13m
kube-system   metrics-server-5985cbc9d7-xcfzk           1/1     Running     0          13m
kube-system   svclb-traefik-3df575a3-ll784              2/2     Running     0          13m
kube-system   traefik-57b79cf995-56jsn                  1/1     Running     0          13m
```
7. 
