# Steps I took to install k3s:

## server setup:
- install ubuntu server as normal
- configure port 1 on second nic to use routes:
``` routes:
     - to: 0.0.0.0/0
       via: 192.168.1.1/24
```
- update, upgrade, and install k3s:
```sudo apt update
   sudo apt upgrade
   curl -sfL https://get.k3s.io | sh -
```
