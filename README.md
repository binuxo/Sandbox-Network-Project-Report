## SANDBOXED NETWORK DIAGRAM<br><br/>
![Network Diagram](doc/Networkdiagram.png) <br><br/>

The diagram illustrates a network setup with two subnets (192.168.24.1 subnet and 192.168.124.1 subnet) and includes several components:
1.	An Ubuntu Desktop on the 192.168.24.0 subnet, connected to a local network.
2.	A Gateway Router with two LAN interfaces, each assigned an IP address in the respective subnets, allows traffic to route between the two subnets.
3.	A Bitnami Application Server is located on the 192.168.124.0 subnet and connects to the gateway for network communication.
4.	The Gateway Router is connected to the external network, providing internet access to both subnets. <br><br/>
This setup ensures that devices in both subnets can communicate with each other through the gateway and access the internet.


IP ADDRESS TABLE FOR A SANDBOXED NETWORK

IP Address Table
<embed src="doc/IPTable.pdf" width="100%" height="400px" />
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
The Ubuntu server acts as the gateway between the two subnets and provides internet access.
Additional of 2 network adapters for the Ubuntu desktop (intnet) and Application server (Intnet 1).
  
 

 
Configure Static IPs on the Network Interfaces
Edit the network configuration file: sudo nano /etc/netplan/00-installer-config.yaml
 

Apply the network changes:
sudo netplan apply 
Enable IP Forwarding: The server must enable IP forwarding to route traffic between the two subnets and the Internet.
Open the sysctl configuration file:
Sudo nano /etc/sysctl.conf
 
Uncomment the line (or add it if not present):
net.ipv4.ip_forward=1
 
Apply the changes:
sudo sysctl -p

Use a Shell Script
Create a shell script and run it:
1.	Create the script file:
nano iptables-setup.sh
2.	Add the commands to the script:
 
3.	Save and close the file (CTRL + O, Enter, CTRL + X).
4.	Make the script executable:
chmod +x iptables-setup.sh
5.	Run the script:
./iptables-setup.sh
D 

This report details the configuration steps required to establish communication between devices on two subnets (192.168.24.1 and 192.168.124.1) and enable internet access. The infrastructure includes the following devices:
1.	Ubuntu 22.04 Desktop (192.168.24.5): Connected to Subnet 192.168.24.1.
2.	Bitnami Application Server (192.168.124.5): Connected to Subnet 192.168.124.1.
3.	Gateway Device (Ubuntu Server): Acts as a gateway between the two subnets and provides internet access.

Steps and Commands for Configuration
Step 2: Configuring the Ubuntu Desktop (192.168.24.5)
1.	Edit the network configuration file: The Ubuntu desktop must be configured to use the IP address 192.168.24.5, subnet mask 255.255.255.0, and the gateway set to the Ubuntu server’s IP address.
 
 

Confirm that the (192.168.24.5) has been assign.
 

Ping the subnet mask (192.168.24.1) and IP address (192.168.24.5) to confirm the connection.
 

Ping the port 8.8.8.8 and google.com to confirm the connection to internet.

 
 
Ping the Bitnami application server 192.168.124.5 to confirm the connection and communication.
 








Step 3: Configuring the Bitnami Application Server (192.168.124.5)
 
Edit the network configuration file: The Bitnami application server must have a static IP configuration, similar to the desktop device.
Edit the /etc/netplan/00-installer-config.yaml file:
sudo nano /etc/netplan/00-installer-config.yaml

Configuration:    
auto enp0s3
iface enp0s3 inet static
addresses 192.168.124.5
netmask 255.255.255.0
dns-nameserver 8.8.8.8
gateway4: 192.168.124.1

Apply the network settings:
sudo netplan apply
        
          
  

Verify the configuration: Check the IP configuration with:
Ip a

 
Ping Test from Bitinami Server
 
Ping Test from Bitnami server to Desktop VM
 
Ping test to confirm communication to the internet.
 
Test connection from the Ubuntu Gateway Server to confirm communication between Ubuntu Desktop VM, Application (Bitnami) VM. 

Ping test from the server to Ubuntu desktop subnet.
 







Ping test to Application VM.
 

Ping test to confirm communication to the internet.
 




The process involved configuring a network setup with two subnets (192.168.24.1 and 192.168.124.1), connecting an Ubuntu 22.04 desktop, a Bitnami application server, and a gateway Ubuntu server. The desktop and application server were configured with static IP addresses, with the gateway Ubuntu server acting as the intermediary for routing traffic between the subnets and providing internet access. Key steps included enabling IP forwarding on the gateway, configuring NAT using iptables, and adding static routes to ensure communication between the subnets. The network devices were tested for connectivity by pinging between each other and verifying internet access.
The configuration was successful, with all devices able to communicate between the two subnets and access the internet without any issues. The Ubuntu server acted as an effective gateway, routing traffic between the subnets and providing NAT functionality for internet access. IP forwarding and NAT configurations were properly set up, and connectivity was verified by successful ping tests across all devices, ensuring that the network setup was fully functional. The network now supports seamless communication and internet connectivity for both the Ubuntu desktop and Bitnami application server.

Link to a screencast of my sandboxed network demonstration.
https://www.loom.com/share/67b2f3c450584574abe1f0ddf80a3e5a?sid=d9f00a47-dceb-4f2e-91ef-753cd783a314
