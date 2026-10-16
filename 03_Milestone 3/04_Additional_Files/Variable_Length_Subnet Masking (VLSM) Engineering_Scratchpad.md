# Variable Length Subnet Masking (VLSM) Engineering Scratchpad

**Module:** CMPG 325 – Computer Networks  
**Project ID:** CMPG325-2026-078  
**Client:** Kgosiemang & Associates Attorneys  
**Base Assigned IP Block:** `192.168.39.0/24`  

---

## 1. Binary Reference & Subnet Masking Cheat Sheet

To verify host requirements and calculate blocks with zero address wastage, the 8 bits in the final octet of the subnet mask are calculated based on their binary position weights:

| Bit Position (Left to Right) | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Decimal Positional Value** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Prefix Length (CIDR)** | `/25` | `/26` | `/27` | `/28` | `/29` | `/30` | `/31` | `/32` |
| **Subnet Mask Last Octet** | `.128` | `.192` | `.224` | `.240` | `.248` | `.252` | `.254` | `.255` |
| **Total Host Block Size (\(2^H\))** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Usable IP Addresses (\(2^H - 2\))** | 126 | 62 | 30 | 14 | 6 | 2 | 0 | 0 |

---

## 2. Structural Subnet Derivation Scratchpad (Sorted Chronologically)

This ledger tracks exactly how the base block `192.168.39.0` is split sequentially across the production network environment without any boundary overlaps:

### 📑 1. VLAN 80 — Future Branch Expansion Block
* **Requirement:** Future scalability constraint
* **Binary Math:** Needs 64 block size → 2⁶ = 64 → 6 Host bits (H), leaving 32 - 6 = 26 Network bits (N).
* **Subnet Mask Binary:** `11111111.11111111.11111111.11000000` → `255.255.255.192`
* **Allocation Calculation:**
  * **Network ID:** `192.168.39.0`
  * **First Usable Host (Gateway):** `192.168.39.1`
  * **Usable Host Scope:** `192.168.39.2` to `192.168.39.62`
  * **Broadcast Address:** `192.168.39.63`

### 📑 2. VLAN 20 — Attorneys Department
* **Requirement:** Large departmental user block
* **Binary Math:** Needs 32 block size → 2⁵ = 32 → 5 Host bits, leaving 32 - 5 = 27 Network bits.
* **Subnet Mask Binary:** `11111111.11111111.11111111.11100000` → `255.255.255.224`
* **Allocation Calculation:**
  * **Network ID:** Next available bit is `192.168.39.64`
  * **First Usable Host (Gateway):** `192.168.39.65`
  * **Usable Host Scope:** `192.168.39.66` to `192.168.39.94`
  * **Broadcast Address:** `192.168.39.95`

### 📑 3. VLAN 40 — Finance Department
* **Requirement:** Small departmental block
* **Binary Math:** Needs 16 block size → 2⁴ = 16 → 4 Host bits, leaving 32 - 4 = 28 Network bits.
* **Subnet Mask Binary:** `11111111.11111111.11111111.11110000` → `255.255.255.240`
* **Allocation Calculation:**
  * **Network ID:** Next available bit is `192.168.39.96`
  * **First Usable Host (Gateway):** `192.168.39.97`
  * **Usable Host Scope:** `192.168.39.98` to `192.168.39.110`
  * **Broadcast Address:** `192.168.39.111`

### 📑 4. VLAN 10 — Management Department
* **Requirement:** Small departmental block
* **Binary Math:** Block size 16 → 2⁴ = 16 → 4 Host bits → Prefix `/28`.
* **Subnet Mask Binary:** `11111111.11111111.11111111.11110000` → `255.255.255.240`
* **Allocation Calculation:**
  * **Network ID:** Next available bit is `192.168.39.112`
  * **First Usable Host (Gateway):** `192.168.39.113`
  * **Usable Host Scope:** `192.168.39.114` to `192.168.39.126`
  * **Broadcast Address:** `192.168.39.127`

### 📑 5. VLAN 30 — Administration Department
* **Requirement:** Small departmental block
* **Binary Math:** Block size 16 → 2⁴ = 16 → 4 Host bits → Prefix `/28`.
* **Subnet Mask Binary:** `11111111.11111111.11111111.11110000` → `255.255.255.240`
* **Allocation Calculation:**
  * **Network ID:** Next available bit is `192.168.39.128`
  * **First Usable Host (Gateway):** `192.168.39.129`
  * **Usable Host Scope:** `192.168.39.130` to `192.168.39.142`
  * **Broadcast Address:** `192.168.39.143`

### 📑 6. VLAN 50 — Reception & Office Peripherals
* **Requirement:** Shared printers/endpoint blocks
* **Binary Math:** Block size 16 → 2⁴ = 16 → 4 Host bits → Prefix `/28`.
* **Subnet Mask Binary:** `11111111.11111111.11111111.11110000` → `255.255.255.240`
* **Allocation Calculation:**
  * **Network ID:** Next available bit is `192.168.39.144`
  * **First Usable Host (Gateway):** `192.168.39.145`
  * **Usable Host Scope:** `192.168.39.146` to `192.168.39.158`
  * **Broadcast Address:** `192.168.39.159`

### 📑 7. VLAN 70 — Secure Wireless LAN mobility Cell
* **Requirement:** Technical Challenge wireless block
* **Binary Math:** Block size 16 → 2⁴ = 16 → 4 Host bits → Prefix `/28`.
* **Subnet Mask Binary:** `11111111.11111111.11111111.11110000` → `255.255.255.240`
* **Allocation Calculation:**
  * **Network ID:** Next available bit is `192.168.39.160`
  * **First Usable Host (Gateway):** `192.168.39.161`
  * **Usable Host Scope:** `192.168.39.162` to `192.168.39.174`
  * **Broadcast Address:** `192.168.39.175`

### 📑 8. VLAN 60 — Central Server Datacenter
* **Requirement:** Isolated local repository segment
* **Binary Math:** Block size 16 → 2⁴ = 16 → 4 Host bits → Prefix `/28`.
* **Subnet Mask Binary:** `11111111.11111111.11111111.11110000` → `255.255.255.240`
* **Allocation Calculation:**
  * **Network ID:** Next available bit is `192.168.39.176`
  * **First Usable Host (Gateway):** `192.168.39.177`
  * **Usable Host Scope:** `192.168.39.178` to `192.168.39.190`
  * **Broadcast Address:** `192.168.39.191`

### 📑 9. Core-to-R1 Transit Line Link
* **Requirement:** Point-to-point router connection network
* **Binary Math:** Needs 4 block size → 2² = 4 → 2 Host bits, leaving 32 - 2 = 30 Network bits.
* **Subnet Mask Binary:** `11111111.11111111.11111111.11111100` → `255.255.255.252`
* **Allocation Calculation:**
  * **Network ID:** Next available bit is `192.168.39.192`
  * **Usable Interface Hosts:** `192.168.39.193` and `192.168.39.194`
  * **Broadcast Address:** `192.168.39.195`

### 📑 10. Core-to-R2 Transit Line Link
* **Requirement:** Point-to-point backup connection network
* **Binary Math:** Block size 4 → 2² = 4 → 2 Host bits → Prefix `/30`.
* **Subnet Mask Binary:** `11111111.11111111.11111111.11111100` → `255.255.255.252`
* **Allocation Calculation:**
  * **Network ID:** Next available bit is `192.168.39.196`
  * **Usable Interface Hosts:** `192.168.39.197` and `192.168.39.198`
  * **Broadcast Address:** `192.168.39.199`

---

## 3. Engineering Validation Proof

\[\text{Total Block Size Allocated} = 64 + 32 + 16 + 16 + 16 + 16 + 16 + 16 + 4 + 4 = 200 \text{ Addresses}\]
\[\text{Remaining Free Unallocated Block Space} = 256 - 200 = 56 \text{ IP Addresses (Fully contiguous from .200 to .255)}\]

