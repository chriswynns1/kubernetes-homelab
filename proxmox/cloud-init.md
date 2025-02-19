# Cloud-Init on Proxmox
## Set up your template VM
Shell into your Proxmox host and download a cloud image:
[Ubuntu Cloud Images](https://cloud-images.ubuntu.com/)
```
wget https://cloud-images.ubuntu.com/oracular/current/oracular-server-cloudimg-amd64.img
```

Create a new vm:
```
qm create 1337 --memory 4096 --core 1 --name ubuntu-cloud --net0 virtio,bridge=vmbr2
```
You can make the machine ID anything you want, just make sure it stands out.
- Change ```--memory``` and ```--core``` to whatever you need
- I chose vmbr2 as my bridge because it's the LAN port on the virtual OPNsense box.

Import the cloud image to the vm:
```
qm disk import 1337 oracular-server-cloudimg-amd64.img local-lvm
```

Attach the disk to the vm:
```
qm set 1337 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-1337-disk-0
```

Add the cloud init drive:
```
qm set 1337 --ide2 local-lvm:cloudinit
```

Make the drive bootable:
```
qm set 1337 --boot c --bootdisk scsi0
```

Add serial console to show us the terminal:
```
qm set 1337 --serial0 socket --vga serial0
```

Now, go back to the web UI and configure the cloud-init drive to use your public SSH key, password, etc.

Right click > Convert to Template

Now you're ready to clone!
