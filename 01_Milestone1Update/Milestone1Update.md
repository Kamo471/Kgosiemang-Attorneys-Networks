Clients requirements it remain the same as appear in my first document
Physical Topology
View my GitHub: https://github.com/Kamo471/Kgosiemang-Attorneys-Networks/tree/main/01_Milestone1Update

In my original physical port layout design, I ran into a critical routing roadblock: the physical transit ports (Gig1/0/1 and Gig1/0/2) on my CORE SWITCH were assigned to subnets that overlapped with the Future Expansion block This caused Cisco Packet Tracer to reject the interfaces on the physical layer.
I have now completely fixed this issue by isolating the transit links safely outside the client ranges, activating the missing 3650 AC Power Supply module inside the hardware bay, and explicitly mapping AccessPoint0 to a dedicated distribution port on Switch 3 to prevent VLAN broadcast leaks 

Here is my updated, fully functional hardware interface connection matrix:
<img width="847" height="641" alt="image" src="https://github.com/user-attachments/assets/1c905fd1-58ff-44a3-b3dc-654112bca6c3" />
<img width="1057" height="618" alt="image" src="https://github.com/user-attachments/assets/a728a1e0-f4d9-4174-83e8-2246535c1d85" />

2. Logical Topology
   
In my original logical topology layout, I ran into a massive network design flaw: local DHCP discover broadcasts (255.255.255.255) were completely trapped inside their own departmental VLAN networks. Because my Local-Server sits in its own isolated subnet on VLAN 60, the broadcast frames sent by client workstations could not cross virtual Layer 3 switch boundaries. This caused all end devices to time out and generate useless APIPA addresses (169.254.x.x).
I have now completely fixed this communication block by configuring an active DHCP Relay Agent (IP helper-address 192.168.39.178) on every single virtual departmental gateway interface (SVI) inside my CORE SWITCH. This policy intercepts the local broadcast packets, rewrites them into targeted unicast frames, and routes them cleanly across the Layer 3 core backbone directly to the server environment.
Additionally, I corrected the routing conflict parameters for CR15 Edge Resilience. My primary and floating backup pathways are now tied to separate, non-overlapping transit networks, allowing the automated failover engine to route outbound traffic smoothly.
________________________________________
My Logical Inter-VLAN Data Flow Diagram

This diagram visualizes how data travels through the virtual subnets, cross-routes through the Core switch relay, and achieves resilient failover out of the firm:
<img width="1063" height="780" alt="image" src="https://github.com/user-attachments/assets/1432233d-4a94-4e1c-9c19-0c7297434544" />

My Automated Failover Path Routing Logic (CR15 Resilience Verification) 
To fulfill the corporate resilience parameters, I injected static routing pathways into my CORE SWITCH
system configuration framework:

. Primary Outbound Pathway Execution: An outbound default route directs all standard office 
internet frames straight down the high-speed transit highway to R1 using a metric / 
Administrative Distance of 1.

 IP route 0.0.0.0 0.0.0.0 192.168.39.194
 
Resilient Floating Backup Pathway Execution: A secondary floating default route points traffic down the 
alternative edge loop to R2 with an elevated Administrative Distance of 10.

 IP route 0.0.0.0 0.0.0.0 192.168.39.198 10
 
Runtime Dynamic Protection: The floating backup route stays hidden from the routing table while the 
primary line is alive. If a physical outage breaks R1, the Core Switch instantly updates its hardware table, 
bringing the R2 backup path online in seconds to keep the office connected.

3. IP ADRESS PLANNING
 <img width="1537" height="456" alt="image" src="https://github.com/user-attachments/assets/4a0fb639-4a75-45a3-9bbb-3aae895bfcac" />

