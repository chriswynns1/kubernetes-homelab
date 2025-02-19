# K3s Cluster Setup
## Server Installation:
1. Install Ubuntu server (in my case, clone a cloud-init template)
2. Update, upgrade, and install k3s as a master node:
```
  sudo apt update
  sudo apt upgrade
  curl -sfL https://get.k3s.io | sh -s - server \ --cluster-init
```
3. Check if installation was successful:
```
chris@k3s-1:~$ sudo kubectl get nodes
NAME    STATUS   ROLES                  AGE   VERSION
node0   Ready    control-plane,master   16m   v1.31.4+k3s1
```
## Agent Installation:
1. First, we need to grab the token from the **server** machine:
```
chris@k3s-1:~$ sudo cat /var/lib/rancher/k3s/server/token
K10089d3021058f513e3f31f789c5...
```
2. Copy the token, ssh into the worker node, and join the cluster:
```
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="agent --server https://[your-master-node-IP] \
--token [your token]" sh -s -
```
3. Grab the kubeconfig from the master:
```
sudo cat /etc/rancher/k3s/k3s.yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJkekNDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdGMyVnkKZG1WeUxXTmhRREUzTXprNU16QXdPVGt3SGhjTk1qVXdNakU1T...
```
4. Copy it to ~/.kube/config using nano (or any text editor)
5. !! Change the server field FROM ```127.0.0.1``` TO the master server IP (10.0.0.106:6443 in my case)
6. Save it and make sure it has the proper permissions:
```
sudo chown chris:chris /home/chris/.kube/config
chmod 600 /home/chris/.kube/config
```
6. After a while, you should be able to view all your nodes:
```
chris@k3s-3:/etc/rancher$ kubectl get nodes -A
NAME    STATUS   ROLES                  AGE   VERSION
k3s-1   Ready    control-plane,master   22h   v1.31.5+k3s1
k3s-2   Ready    <none>                 21h   v1.31.5+k3s1
k3s-3   Ready    <none>                 35m   v1.31.5+k3s1
```
