Technical Design and Configuration Report (Milestone 3 Final Submission)
Module: CMPG 325 – Computer Networks 
Project ID: CMPG325-2026-078 
Client ID: CLI-078 
Student: MOHAPANELE, KAMO (45737614) 
Client: Kgosiemang & Associates Attorneys 
Location: Mahikeng, South Africa 
Submission Date: 16 October 2026 
________________________________________
1. Executive Summary & Business Requirements
Kgosiemang & Associates Attorneys is a professional legal services firm operating out of Mahikeng. The firm requires a highly available, securely segmented campus network infrastructure to protect sensitive client records, ensure uninterrupted data centre access, and support daily legal business operations.
This final technical report details the comprehensive implementation of a tailored corporate network addressing the following core directives:
•	Addressing Allocation Bounds: Efficient division of the assigned 192.168.39.0/24 IPv4 block using Variable Length Subnet Masking (VLSM) to prevent IP exhaustion or leakage.
•	Logical Domain Isolation: Implementation of dedicated Virtual Local Area Networks (VLANs) to separate corporate organizational functions.
•	Gateway Edge Resilience (CR15): Deployment of an automated, dual-homed internet gateway infrastructure ensuring runtime failover capabilities.
•	Assigned Technical Challenge Integration: Integration of a secure enterprise Wireless LAN (WLAN) cell anchored by a central Dynamic Host Configuration Protocol (DHCP) allocation scope.
•	Scalable Provisioning: Allocation of structural address blocks to seamlessly integrate an entire upcoming departmental branch without disturbing active networks.
________________________________________
2. Hierarchical Topology Design & Architecture
The campus topology adopts a formal hierarchical architecture engineered within Cisco Packet Tracer. By decoupling local access loops from layer-3 core processes, the infrastructure isolates structural faults and maximizes backbone frame throughput.
2.1 Interface Connection Mappings
Based on the verified project hardware wiring matrix, the physical infrastructure is cabled using the exact port distributions listed below:
 <img width="940" height="517" alt="image" src="https://github.com/user-attachments/assets/d9c1e9db-5d20-4e4f-812b-03a9b3e30770" />

Edge and Core Backbone Links 
•	Internet 1 (Ethernet 6) connects to ISP1 (Gig0/0/0) via Copper Straight-Through.
•	Internet 2 (Ethernet 6) connects to ISP2 (Gig0/0/0) via Copper Straight-Through.
•	ISP1 (Gig0/0/1) connects to R1 (Gig0/0/0) via Copper Straight-Through.
•	ISP2 (Gig0/0/1) connects to R2 (Gig0/0/0) via Copper Straight-Through.
•	R1 (Gig0/0/1) connects to CORE SWITCH (Gig1/0/1) via Copper Straight-Through (Primary Transit Network: 192.168.39.192/30).
•	R2 (Gig0/0/1) connects to CORE SWITCH (Gig1/0/2) via Copper Straight-Through (Backup Transit Network: 192.168.39.196/30).
Multi-Floor Core Trunk Highways CORE SWITCH (Gig1/0/22) connects to SW1 (Gig0/1) via Copper Straight-Through (IEEE 802.1Q Native Trunk Line).
•	CORE SWITCH (Gig1/0/23) connects to SW2 (Gig0/1) via Copper Straight-Through (IEEE 802.1Q Native Trunk Line).
•	CORE SWITCH (Gig1/0/24) connects to SW3 (Gig0/1) via Copper Straight-Through (IEEE 802.1Q Native Trunk Line).

Access Layer Departmental Assignments SW1 (FastEth0/1) connects to Mgmt-PC (FastEth0) | Access Link assigned to Management (VLAN 10).
•	SW1 (FastEth0/2) connects to Finance-PC (FastEth0) | Access Link assigned to Finance (VLAN 40).
•	SW2 (FastEth0/1) connects to Attorneys-PC (FastEth0) | Access Link assigned to Attorneys (VLAN 20).
•	SW2 (FastEth0/2) connects to Local-Server (FastEth0) | Server Access Link bound to Datacenter (VLAN 60).
•	SW3 (FastEth0/1) connects to Admin-PC (FastEth0) | Access Link assigned to Administration (VLAN 30).
•	SW3 (FastEth0/2) connects to Reception-PC (FastEth0) | Access Link assigned to Reception (VLAN 50).
•	SW3 (FastEth0/15) connects to Network-Printer (FastEth0) | Peripherals Access Link bound to Reception (VLAN 50).
•	SW3 (FastEth0/20) connects to AccessPoint0 (Port 0) | Static Distribution Link assigned to WLAN (VLAN 70).
•	AccessPoint0 (RF Antenna) bridges to Wireless-Laptop (Internal Card) | 2.4GHz Over-the-Air Wireless Mobility Access Cell.
________________________________________
3. Variable Length Subnet Masking (VLSM) IP Addressing Plan
The assigned network space 192.168.39.0/24 has been subnetted to match the active runtime routing parameters of your Core Switch, preventing address overlaps while establishing secure gateways:
 <img width="940" height="364" alt="image" src="https://github.com/user-attachments/assets/8b06cba8-4b1e-4168-90a1-11cb96ade817" />

4. Technical Configuration and Core Services
4.1 Layer-3 Core Routing Engine & Gateway Layout
Switch Virtual Interfaces (SVIs) are established on the Cisco Catalyst 3650 Multilayer Switch (CORE_SWITCH) to enable wire-speed Inter-VLAN routing across the corporate domain. This design provides complete logical isolation between law firm segments while allowing necessary traffic to cross layer-3 boundaries smoothly.
4.2 Automated Edge Path Routing Engine (CR15 Resilience)
To satisfy the requirements of Change Request CR15, the firm is provisioned with dual-homed edge routes via primary router R1 and secondary resilient router R2. Static default routing paths were injected into the CORE_SWITCH engine to enforce automatic structural failover:
•	Primary Outbound Highway (via R1): ip route 0.0.0.0 0.0.0.0 192.168.39.194 backed by a static Administrative Distance of 1.
•	Resilient Floating Backup Pathway (via R2): ip route 0.0.0.0 0.0.0.0 192.168.39.198 10 backed by an elevated Administrative Distance of 10.
•	Operational Logic: The floating route remains hidden from the active forwarding table during standard operation. If a physical line rupture or upstream link loss disrupts the R1 connection path, the core routing engine immediately updates its hardware topology, activating the secondary R2 line within seconds to maintain persistent firm uptime.

4.3 DHCP Relay Agent and Central Server Pool Execution
 <img width="1040" height="686" alt="image" src="https://github.com/user-attachments/assets/99843624-2c04-40f3-912c-a3774c72d088" />

The central firm repository (Local-Server) resides securely within the isolated Server Datacenter segment (VLAN 60). To allow broadcast DHCP requests from workstations to cross virtual router interfaces, active DHCP Relay Agents were configured across every active departmental SVI gateway using the Cisco IOS ip helper-address 192.168.39.178 command string.

This mechanism intercepts inbound local broadcast strings, converts them into targeted unicast frames, and routes them cleanly across the Layer 3 core backbone directly to the server environment. The central server hosts matching data pools (such as Wireless LAN), distributing appropriate network credentials based on the source interface of the incoming relay string.
4.4 Enterprise Peripheral Management
Shared enterprise components like printers require persistent host addressing. The Network-Printer bound to SW3 interface FastEth0/15 was removed from the dynamic DHCP pool scope. It was manually provisioned with a fixed static address within the VLAN 50 block to prevent lease rotation and ensure consistent printing availability across all departments.
________________________________________
5. Verification, Testing, and Troubleshooting Evidence
End-to-end operational functionality and network layer configuration validation were confirmed through rigorous, systematic trace testing inside the live simulation environment 
<img width="414" height="511" alt="image" src="https://github.com/user-attachments/assets/098f64ab-6a7d-4d3c-8c5a-ac2271c3c274" />
<img width="480" height="514" alt="image" src="https://github.com/user-attachments/assets/9efa450c-fb9b-4b79-952a-c3d3d8610804" />
<img width="466" height="539" alt="image" src="https://github.com/user-attachments/assets/ef2a511f-c5df-4bb3-835b-436b6c2c4591" />
<img width="446" height="534" alt="image" src="https://github.com/user-attachments/assets/0c0b5e83-40cc-4c72-9000-3f8d7a20b3fe" />
<img width="459" height="580" alt="image" src="https://github.com/user-attachments/assets/381c7762-5184-495f-ad2a-43bee4f61b3b" />
<img width="448" height="582" alt="image" src="https://github.com/user-attachments/assets/e75dd1ea-b1ca-4aa6-851c-7547d71c29db" />
                 
  I forgot to test wireless Laptop in milestone 2                                                     
5.1 Dynamic Host Lease Acquisition Evidence
All active corporate endpoint systems successfully initiated link-layer negotiations and completed 4-way DHCP transactions to pull their planned network attributes:
•	Management-PC: Successfully leased IP 192.168.39.114 | Subnet Mask 255.255.255.240 | Default Gateway 192.168.39.113 
•	Attorneys-PC: Successfully leased IP 192.168.39.66 | Subnet Mask 255.255.255.224 | Default Gateway 192.168.39.65 
•	Admin-PC: Successfully leased IP 192.168.39.131 | Subnet Mask 255.255.255.240 | Default Gateway 192.168.39.129 
•	Finance-PC: Successfully leased IP 192.168.39.99 | Subnet Mask 255.255.255.240 | Default Gateway 192.168.39.97 
•	Reception-PC: Successfully leased IP 192.168.39.147 | Subnet Mask 255.255.255.240 | Default Gateway 192.168.39.145 
•	Wireless-Laptop (Technical Challenge Verification): Successfully established secure 802.11 media association using WPA2-PSK protection (Kgosiemang_WLAN / Kgosiemang@2026). The laptop successfully pulls IP 192.168.39.162, Subnet Mask 255.255.255.240, Default Gateway 192.168.39.161.
5.2 Network Peripheral Fixed Allocation Verification
  

•	Network-Printer: Fixed static allocation verified. IPv4 Address: 192.168.39.146, Subnet Mask: 255.255.255.240, Default Gateway: 192.168.39.145. Link status lights register active layer-1 execution

5.3 ICMP Data Plane Inter-VLAN Routing Logs
 <img width="470" height="462" alt="image" src="https://github.com/user-attachments/assets/683c089e-3e87-4931-b0ae-4a7a22fbb089" />
 <img width="435" height="461" alt="image" src="https://github.com/user-attachments/assets/b2c0882d-f54c-4605-a5dd-38ad79f7c7d1" />
 <img width="463" height="437" alt="image" src="https://github.com/user-attachments/assets/12913518-f91d-4839-bd2c-a0f3c7f9b772" />
 <img width="443" height="431" alt="image" src="https://github.com/user-attachments/assets/73cf3a3f-4542-498b-9876-39e7e3f7a65c" />
 <img width="423" height="383" alt="image" src="https://github.com/user-attachments/assets/5dfa752b-6359-4960-8a71-c4e9334c594c" />
 <img width="471" height="377" alt="image" src="https://github.com/user-attachments/assets/2d626076-1377-4ea7-aaef-00ed78a6f4ea" />        
To verify comprehensive data path connectivity across internal network boundaries, echo request ping strings were executed from separate endpoint segments to the central database (192.168.39.178):
•	Management-PC to Local-Server (ping 192.168.39.178): 4 packets sent, 4 packets received | 0% packet loss.
•	Finance-PC to Local-Server (ping 192.168.39.178): 4 packets sent, 4 packets received | 0% packet loss.
•	Attorneys-PC to Local-Server (ping 192.168.39.178): 4 packets sent, 4 packets received | 0% packet loss.
•	Admin-PC to Local-Server (ping 192.168.39.178): 4 packets sent, 4 packets received | 0% packet loss.
•	Reception-PC to Local-Server (ping 192.168.39.178): 4 packets sent, 4 packets received | 0% packet loss.
•	Wireless-Laptop to Local-Server (ping 192.168.39.178): 4 packets sent, 4 packets received | 0% packet loss.
________________________________________
6. Design Challenges, Mistakes, and Troubleshooting Logs
Throughout the lifecycle of building and integrating this network architecture, several critical hardware and software implementation mistakes were made. Identifying, analyzing, and systematically solving these errors forms a key part of our engineering validation process:
6.1 The SVI Subnet Overlap Deadlock
•	The Mistake: During initial setup, broad /24 and loose /25 masks from early design phases were left configured on several switch virtual interfaces. When attempting to add the new Wireless LAN segment gateway (interface Vlan70), the console threw a fatal rejection error: % 192.168.39.112 overlaps with Vlan10. Cisco IOS blocked the allocation because the old masks were claiming addresses reserved for other networks.
•	The Correction: To break the deadlock, a global interface clear was executed on CORE_SWITCH. The conflicting interfaces were completely stripped using the no ip address command string. The entire addressing architecture was then re-applied strictly according to the narrow, non-overlapping boundaries specified in our new VLSM plan, allowing all gateways to wake up cleanly.
6.2 The Missing Backbone VLAN Trunk Allowances
•	The Mistake: When the wireless laptop was first brought online, its over-the-air link to AccessPoint0 was functional, but its IP search timed out with a DHCP Failed error, assigning a useless 169.254.x.x APIPA address. The layer-2 broadcast frames were being dropped before crossing subnets.
•	The Correction: Diagnostic checks revealed that while the CORE_SWITCH had a proper DHCP relay address configured, the intermediate backbone uplink port (Gig0/1 on SW3) was missing an operational trunk policy. The port was entering the default VLAN 1 mode instead of carrying multi-VLAN traffic. Entering the CLI commands switchport mode trunk and switchport trunk allowed vlan all on SW3 successfully opened the data pipeline, allowing the wireless packets to cross virtual domains to the server repository 

6.3 Access Layer Port Boundary Leaks
•	The Mistake: During intermediate integration testing, configuring the wireless laptop's network settings caused a cascading failure that dropped the wired Management-PC into a DHCP Failed loop. Stripping and resetting the virtual interfaces had inadvertently dropped the core helper configurations on other active lines.
•	The Correction: The core interface was systematically verified by checking the switch's status with show ip interface brief. Port assignments on the local floor switch (SW3) were then explicitly secured by isolating interface Fast Ethernet 0/20 to access only VLAN 70 via the command switchport access vlan 70. Finally, the critical routing helper pathing was explicitly restored on the Core Switch using the ip helper-address 192.168.39.178 script line on all departmental gates, restoring stable IP allocation firm-wide.
________________________________________
7. GitHub Portfolio Architecture & Evidence Links
The complete engineering design lifecycle and simulation assets have been committed to the public version control repository.
•	Main Repository: https://github.com/Kamo471/Kgosiemang-Attorneys-Networks
•	Milestone 3 Final Directory: https://github.com/Kamo471/Kgosiemang-Attorneys-Networks/tree/main/03_Milestone%203 

•	Cisco Packet Tracer File (.pkt): https://github.com/Kamo471/Kgosiemang-Attorneys-Networks/tree/main/03_Milestone%203/01_%20Packet_Tracer_Project(.pkt) 


