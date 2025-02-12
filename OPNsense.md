# OPNsense setup
## Install OPNsense
In my case, I installed OPNsense on my Proxmox virtual environment. Before you can install the ISO, you need to setup some bridges.

1. We need to have two bridges. One for WAN, and one for LAN.
2. Create a virtual bridge for each NIC.
3. Use these virtual brides as network interfaces on the OPNsense box.

### Setting it up
After installing OPNsense and setting the network interfaces, connect everything as follows:

1. ISP ROUTER -> management interface (motherboard NIC)
2. Set static interface for LAN (can do this through the console on Proxmox)
- I set the LAN interface to use 10.0.0.1/24, subnet mask is 255.255.255.0
3. Connect LAN to switch
4. Connect WAN to ISP
5. Connect devices to switch

Now, we can configure OPNsense using the web gui:
1. Visit ```https://10.0.0.1``` or whatever you set your LAN IP to
2. Follow the wizard 🧙‍♂️

