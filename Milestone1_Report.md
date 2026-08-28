**Course Code:** CMPG 325  
**Project ID:** CMPG325-2026-078  
**Client Name:** Kgosiemang & Associates Attorneys (Mahikeng)  
**Assigned Network Constraint Block:** 192.168.39.0/24  
**Date:** 28 August 2026  

---

## 1. Client Requirements & Design Assumptions
### 1.1 Requirements identified from the project brief:
* The network must use the assigned addressing block **192.168.39.0/24**. 
* The network must provide appropriate connectivity and network services. 
* The network must include appropriate routers, switches, end devices and other required network nodes. 
* The network must be designed and simulated using Cisco Packet Tracer. 
* The network must include a Wireless LAN with Access Point integration and coverage. 
* The Wireless LAN must be configured, verified and demonstrated. 
* The network must accommodate the addition of a new department next year. 
* The network must incorporate CR15, which requires a second Internet connection for resilience. 
* End-to-end connectivity must be tested. 
* The assigned Wireless LAN challenge must be tested. 
* Important design decisions, configuration, testing and troubleshooting must be documented in the GitHub portfolio.

### 1.2 Design Assumptions
* The organisation consists of Management, Attorneys, Administration, Finance and Reception departments. 
* Each department is assumed to have an appropriate number of desktop computers. 
* Attorneys and selected management staff are assumed to use laptops. 
* Network printers are assumed to be available for document-related activities. 
* An internal server is assumed to provide required network services. 
* Departments are logically separated using VLANs and separate IP subnets. 
* A dedicated Wireless LAN is provided for wireless users. 
* A dedicated subnet is reserved for the future department. 
* Two Internet connections are included to satisfy CR15 and provide resilience. 
* The proposed network is an engineered solution based on the project scenario and does not claim to reproduce the actual network of Kgosiemang & Associates Attorneys.

* ## 2. Physical Topology

The physical topology is implemented using a Hierarchical Cisco Enterprise Network design. A Multilayer Layer 3 Core Switch serves as the backbone routing engine, connecting downstream access layer switches to the resilient dual-router edge infrastructure.

### 2.1 Workspace Network Layout Snapshot
<img width="1066" height="834" alt="image" src="https://github.com/user-attachments/assets/5d762bfc-e480-4e9c-88c5-25c916e524bb" />
### 2.2 Workspace Interconnection Diagram
* <img width="620" height="551" alt="image" src="https://github.com/user-attachments/assets/00b2f3d7-9ae3-4ba9-9a7e-f18f83ae6c58" />

```
### 2.3 Physical Port Interface Assignment Matrix

| Uplink Source Device Node | Outbound Port | Downstream Destination Node | Inbound Port | Cable Medium Specification | Intended Design Role / Operational Purpose |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Cloud-PT Internet1** | Ethernet 6 | **ISR4331 ISP1** | Gig0/0/1 | Copper Straight-Through | Upstream primary injection path link |
| **Cloud-PT Internet2** | Ethernet 6 | **ISR4331 ISP2** | Gig0/0/1 | Copper Straight-Through | Upstream resilient backup injection link |
| **ISR4331 ISP1** | Gig0/0/0 | **ISR4331 R1** | Gig0/0/1 | Copper Straight-Through | Public facing WAN boundary carrier link |
| **ISR4331 ISP2** | Gig0/0/0 | **ISR4331 R2** | Gig0/0/1 | Copper Straight-Through | Public facing redundant WAN carrier link |
| **ISR4331 R1** | Gig0/0/0 | **3650 24PS CORE SWITCH** | Gig1/0/1 | Copper Straight-Through | Primary `/30` Backbone Transit Highway link |
| **ISR4331 R2** | Gig0/0/0 | **3650 24PS CORE SWITCH** | Gig1/0/2 | Copper Straight-Through | Backup `/30` Resilient Transit Highway link |
| **3650 24PS CORE SWITCH** | Gig1/0/3 | **2960-24TT SW1** | Gig0/1 | Copper Straight-Through | IEEE 802.1Q Native Trunk uplink line |
| **3650 24PS CORE SWITCH** | Gig1/0/4 | **2960-24TT SW2** | Gig0/1 | Copper Straight-Through | IEEE 802.1Q Native Trunk uplink line |
| **3650 24PS CORE SWITCH** | Gig1/0/5 | **2960-24TT SW3** | Gig0/1 | Copper Straight-Through | IEEE 802.1Q Native Trunk uplink line |
| **2960-24TT SW1** | FastEthernet0/1 | **PC-PT Mgmt-PC** | FastEthernet0 | Copper Straight-Through | Access link assigned to Management (VLAN 10) |
| **2960-24TT SW1** | FastEthernet0/11| **PC-PT Finance-PC** | FastEthernet0 | Copper Straight-Through | Access link assigned to Finance (VLAN 40) |
| **2960-24TT SW2** | FastEthernet0/1 | **PC-PT Attorneys-PC**| FastEthernet0 | Copper Straight-Through | Access link assigned to Attorneys (VLAN 20) |
| **2960-24TT SW2** | FastEthernet0/24| **Server-PT Local-Server**| FastEthernet0 | Copper Straight-Through | Server access link bound to Datacenter (VLAN 60) |
| **2960-24TT SW3** | FastEthernet0/1 | **PC-PT Admin-PC** | FastEthernet0 | Copper Straight-Through | Access link assigned to Administration (VLAN 30) |
| **2960-24TT SW3** | FastEthernet0/11| **PC-PT Reception-PC**| FastEthernet0 | Copper Straight-Through | Access link assigned to Reception (VLAN 50) |
| **2960-24TT SW3** | FastEthernet0/15| **Printer-PT Network-Printer**| FastEthernet0| Copper Straight-Through| Peripherals access link bound to VLAN 50 |
| **2960-24TT SW3** | FastEthernet0/20| **AccessPoint-PT Access Point0**| Port 0 | Copper Straight-Through | Static distribution link assigned to WLAN (VLAN 70) |

---

## 3. Logical Topology & Routing Design

The internal inter-VLAN corporate traffic is maintained directly on the multilayer core backbone. Although frame pipelines run across common physical trunks, layer-3 virtual boundaries isolate individual broadcast domains.

### 3.1 Flowchart Layout
```text
  [ Internal Departmental VLANs ]
  (10, 20, 30, 40, 50, 60, 70)
                │
                ▼ (802.1Q Encapsulation)
    [ 3650 Multilayer Core Switch ] ──► (Performs local layer-3 routing)
                │
        ┌───────┴───────┐
        │               │
        ▼               ▼
   (Primary Path)  (Floating Backup Path)
     Metric: 1       Metric: 10
        │               │
  Transit .160/30  Transit .164/30
        │               │
        ▼               ▼
     [ R1 ]          [ R2 ]
        │               │
        ▼               ▼
    [ ISP 1 ]       [ ISP 2 ]
```

### 3.2 Automated Edge Path Routing Flow (CR15 Resilience)
* **Primary Gateway Definition:** The Core Switch pathways point outbound corporate internet frames directly down the primary transit line to **R1** (`ip route 0.0.0.0 0.0.0.0 192.168.39.162`) with an Administrative Distance of 1.
* **Floating Backup Definition:** A second floating backup route points traffic down the secondary line to **R2** (`ip route 0.0.0.0 0.0.0.0 192.168.39.166 10`) with an elevated Administrative Distance of 10.
* **Failover Engine Mechanics:** The backup route remains inactive while the primary gateway connection is alive. If a hardware outage strikes R1, the Core Switch instantly fails over to R2 and ISP2. This meets the CR15 redundancy parameters perfectly.

---

## 4. IP Addressing Plan

The assigned network space `192.168.39.0/24` has been custom-subnetted using Variable Length Subnet Masking (VLSM) to accommodate departmental host capacities, reserve expansion space, and establish resilient backbone pathways.

| VLAN ID | Department / Function | Host Capacity | Subnet Address | Subnet Mask | Assignable Host Range | Default Gateway |
| :---: | :--- | :---: | :--- | :--- | :--- | :--- |
| **10** | Management | 10 | 192.168.39.0/28 | 255.255.255.240 | 192.168.39.2 – 192.168.39.14 | 192.168.39.1 |
| **20** | Attorneys | 25 | 192.168.39.16/27 | 255.255.255.224 | 192.168.39.18 – 192.168.39.46 | 192.168.39.17 |
| **30** | Administration | 8 | 192.168.39.48/28 | 255.255.255.240 | 192.168.39.50 – 192.168.39.62 | 192.168.39.49 |
| **40** | Finance | 12 | 192.168.39.64/28 | 255.255.255.240 | 192.168.39.66 – 192.168.39.78 | 192.168.39.65 |
| **50** | Reception | 5 | 192.168.39.80/28 | 255.255.255.240 | 192.168.39.82 – 192.168.39.94 | 192.168.39.81 |
| **60** | Local Servers | 2 | 192.168.39.96/28 | 255.255.255.240 | 192.168.39.98 – 192.168.39.110 | 192.168.39.97 |
| **70** | Wireless LAN | 5 | 192.168.39.112/28 | 255.255.255.240 | 192.168.39.114 – 192.168.39.126 | 192.168.39.113 |
| **80** | Future Expansion | 50 | 192.168.39.128/26 | 255.255.255.192 | 192.168.39.130 – 192.168.39.190 | 192.168.39.129 |
| **──** | Transit (Core-to-R1) | 2 | 192.168.39.160/30 | 255.255.255.252 | 192.168.39.161 – 192.168.39.162 | N/A |
| **──** | Transit (Core-to-R2) | 2 | 192.168.39.164/30 | 255.255.255.252 | 192.168.39.165 – 192.168.39.166 | N/A |



