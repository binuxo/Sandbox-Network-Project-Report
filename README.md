## SANDBOXED NETWORK DIAGRAM<br><br/>
![Network Diagram](doc/Networkdiagram.png) <br><br/>

The diagram illustrates a network setup with two subnets (192.168.24.1 subnet and 192.168.124.1 subnet) and includes several components:
1.	An Ubuntu Desktop on the 192.168.24.0 subnet, connected to a local network.
2.	A Gateway Router with two LAN interfaces, each assigned an IP address in the respective subnets, allows traffic to route between the two subnets.
3.	A Bitnami Application Server is located on the 192.168.124.0 subnet and connects to the gateway for network communication.
4.	The Gateway Router is connected to the external network, providing internet access to both subnets. <br><br/>
This setup ensures that devices in both subnets can communicate with each other through the gateway and access the internet.


IP ADDRESS TABLE FOR A SANDBOXED NETWORK

<embed src="IPTable.pdf" width="100%" height="400px" />
<br><br/>
<br><br/>				

The IP table configuration ensures proper communication and internet access between the devices on the two subnets (192.168.24.1 and 192.168.124.1). It includes the following rules:<br><br/>
1.	**NAT (Network Address Translation):** The gateway device uses NAT to masquerade outgoing subnet traffic, allowing internet access through the external interface (eth0). This is achieved with the command:<br><br/>
_sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE_ <br><br/>
2.	**Forwarding Rules:** The gateway allows traffic to be forwarded between the two subnets and the internet. It accepts established and related connections, ensuring the devices can communicate with each other and the internet. The following rules are applied:<br><br/>
_sudo iptables -A FORWARD -i enp0s8 -o enp0s9 -m state --state RELATED,ESTABLISHED -j ACCEPT_
_sudo iptables -A FORWARD -i enp0s9 -o enp0s8 -j ACCEPT_<br><br/>
3.	**Persistence:** The iptables configuration is saved using iptables-persistent, ensuring the rules remain in place after a reboot.<br><br/>
This configuration allows the devices to communicate across subnets and access the internet via the gateway router, ensuring network functionality.<br><br/>


### Step 1: Configuring the Gateway (Ubuntu Server)
The Ubuntu server acts as the gateway between the two subnets and provides internet access.<br><br/>
Additional of 2 network adapters for the Ubuntu desktop (intnet) and Application server (Intnet 1).
  
![Configuring Gateway Server](doc/US1.png) <br><br/>
![Configuring Gateway Server](doc/US2.png) <br><br/>
![Configuring Gateway Server](doc/US3.png) <br><br/>
#### Configure Static IPs on the Network Interfaces
**Edit the network configuration file:**<br><br/>
_sudo nano /etc/netplan/00-installer-config.yaml_ <br><br/>
![Configuring Static IPs](doc/US4.png) <br><br/>

**Apply the network changes:**
sudo netplan apply 
**Enable IP Forwarding:** The server must enable IP forwarding to route traffic between the two subnets and the Internet.
**Open the sysctl configuration file:**
_Sudo nano /etc/sysctl.conf_
![Apply the configuration](doc/US5.png) <br><br/>
 
**Uncomment the line (or add it if not present):**<br><br/>
_net.ipv4.ip_forward=1_ <br><br/>
![IP Forwarding](doc/US6.png) <br><br/>
 
**Apply the changes:**<br><br/>
_sudo sysctl -p_

### Use a Shell Script
Create a shell script and run it:
1.	**Create the script file:**<br><br/>
_nano iptables-setup.sh_<br><br/>
2.	**Add the commands to the script:**<br><br/>
 ![IP Forwarding](doc/US7.png) <br><br/>
3.	**Save and close the file** _(CTRL + O, Enter, CTRL + X)._
4.	**Make the script executable:**
_chmod +x iptables-setup.sh_
5.	**Run the script:**
_./iptables-setup.sh_<br><br/>
![Making the script executable](doc/US8.png) <br><br/>

This report details the configuration steps required to establish communication between devices on two subnets (192.168.24.1 and 192.168.124.1) and enable internet access. The infrastructure includes the following devices:<br><br>
1.	**Ubuntu 22.04 Desktop (192.168.24.5):** Connected to Subnet 192.168.24.1.
2.	**Bitnami Application Server (192.168.124.5):** Connected to Subnet 192.168.124.1.
3.	**Gateway Device (Ubuntu Server):** Acts as a gateway between the two subnets and provides internet access.

### Steps and Commands for Configuration<br><br/>
**Step 2: Configuring the Ubuntu Desktop (192.168.24.5)**
1.	**Edit the network configuration file:** The Ubuntu desktop must be configured to use the IP address 192.168.24.5, subnet mask 255.255.255.0, and the gateway set to the Ubuntu server’s IP address.<br><br/>
![Configuring Ubuntu Desktop](doc/UD1.png) <br><br/>
![Assigning Static IP address](doc/UD2.png) <br><br/>
**Confirm that the (192.168.24.5) has been assign.**<br><br/>
![Confirm the IP address](doc/UD3.png) <br><br/>

**Ping the subnet mask (192.168.24.1) and IP address (192.168.24.5) to confirm the connection.**<br><br/>
![Ping the subnet mask and IP address](doc/UD4.png) <br><br/>
**Ping the port 8.8.8.8 and google.com to confirm the connection to internet.**<br><br/>
![Ping port 8.8.8.8](doc/UD5.png) <br><br/>
![Ping google.com](doc/UD6.png) <br><br/>
**Ping the Bitnami application server 192.168.124.5 to confirm the connection and communication.**<br><br/>
![Ping bitnami subnet and IP address](doc/UD7.png) <br><br/>

### Step 3: Configuring the Bitnami Application Server (192.168.124.5)<br><br/>
![Configuring the Bitnami Application Server](doc/B1.png) <br><br/>
 
**Edit the network configuration file:** The Bitnami application server must have a static IP configuration, similar to the desktop device.<br><br/>
**Edit the:** _/etc/netplan/00-installer-config.yaml file:_<br><br/>
_sudo nano /etc/netplan/00-installer-config.yaml_

**Configurations:** <br><br/>
_auto enp0s3_
_iface enp0s3 inet static_
_addresses 192.168.124.5_
_netmask 255.255.255.0_
_dns-nameserver 8.8.8.8_
_gateway4: 192.168.124.1_<br><br/>

**Apply the network settings:**<br><br/>
_sudo netplan apply_<br><br/>
![Configure Static IP Address on Bitnami Server](doc/B2.png) <br><br/>

**Verify the configuration: Check the IP configuration with:**<br><br/>
_Ip a_<br><br/>
![Verifying the IP configuration](doc/B3.png) <br><br/>
 
**Ping Test from Bitinami Server**<br><br/>
![Ping Test from Bitnami Server](doc/B4.png) <br><br/>

**Ping Test from Bitnami server to Desktop VM**<br><br/>
![Ping Test to Desktop VM](doc/B5.png) <br><br/>

**Ping test to confirm communication to the internet.**<br><br/>
![Ping Test to confirm access to internet](doc/B6.png) <br><br/>

**Test connection from the Ubuntu Gateway Server to confirm communication between Ubuntu Desktop VM, Application (Bitnami) VM.**<br><br/>
![Ping Test from Ubuntu Server to Ubuntu Desktop and Application Bitnami Server](doc/B7.png) <br><br/>
**Ping test from the server to Ubuntu desktop subnet.**<br><br/>
![Ping Test to Ubuntu Desktop subnet](doc/B8.png) <br><br/>
**Ping test to Application VM.**<br><br/>
![Ping Test to Application VM](doc/B9.png) <br><br/>
**Ping test to confirm communication to the internet.**<br><br/>
 ![Ping Test to confirm communication to the internet](doc/B10.png) <br><br/>

## SUMMARY<BR><BR/>
The process involved configuring a network setup with two subnets **(192.168.24.1 and 192.168.124.1),** connecting an **Ubuntu 22.04 desktop,** a **Bitnami application server,** and a **gateway Ubuntu server.** The desktop and application server were configured with static IP addresses, with the gateway Ubuntu server acting as the intermediary for routing traffic between the subnets and providing internet access. Key steps included enabling IP forwarding on the gateway, configuring NAT using iptables, and adding static routes to ensure communication between the subnets. The network devices were tested for connectivity by pinging between each other and verifying internet access.
The configuration was successful, with all devices able to communicate between the two subnets and access the internet without any issues. The Ubuntu server acted as an effective gateway, routing traffic between the subnets and providing NAT functionality for internet access. IP forwarding and NAT configurations were properly set up, and connectivity was verified by successful ping tests across all devices, ensuring that the network setup was fully functional. The network now supports seamless communication and internet connectivity for both the Ubuntu desktop and Bitnami application server.
<br><br/>


<figure>
  <video width="640" height="360" controls>
    <source src="doc/Sandboxed Network Video.mp4" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <figcaption>Video : Screencast of my sandboxed network demonstration</figcaption>
</figure>
<br><br/>

[Link to a screencast of my sandboxed network demonstration.](https://www.loom.com/share/67b2f3c450584574abe1f0ddf80a3e5a?sid=d9f00a47-dceb-4f2e-91ef-753cd783a314)
