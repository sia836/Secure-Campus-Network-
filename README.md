# Secure Campus Network

A Secure Campus Network is a comprehensive network simulated in Cisco Packet Tracer. The architecture utilizes a 3-tier hierarchical design featuring Multi-Area OSPF that segregates branch and data center traffic across distinct areas to ensure rapid convergence, VLAN segmentation via Router-on-a-Stick (ROAS), dynamic host configuration via DHCP, perimeter NAT/PAT, and ACL-based access control to shield central servers from unauthorized departmental access.
# Table of Contents

* [Features](#features)
* [Network Architecture and Topology](#network-architecture-and-topology)
* [Verification and Testing](#verification-and-testing)
  
# Features
  
* Department Isolation (VLANs & ROAS): Keeps departmental traffic (Sales, Guest, IT) safely separated into independent subnets using single router sub-interfaces to prevent broadcast congestion.
  
* Smart Perimeter Security (Extended ACLs): Blocks unauthorized departments from accessing sensitive central servers right at the entry port, saving backbone bandwidth.
  
* Scalable Dynamic Routing (Multi-Area OSPF): Connects the network across Area 0 (Core), Area 1, and Area 2 to guarantee fast route convergence without overloading the routers.
  
* Automated IP Assignment (DHCP): Instantly provisions IP addresses, subnet masks, and default gateways to all connected PCs across every department without manual setup.
  
* Cost-Efficient Internet Access (PAT / NAT Overload): Translates multiple private internal IP addresses into a single public address to mirror standard enterprise internet access.

# Network Architecture And Topology

The Campus Network topology uses a hierarchical 3-tier architecture design from a parent 172.16.0.0/21 address block using VLSM to optimize the IP space, contains broadcast and isolates administrative domains.
<img width="1796" height="857" alt="Topology" src="https://github.com/user-attachments/assets/e0b56fab-d270-42aa-a7fc-34c86f23972f" />
| Location / Area | Department / Role | VLAN ID | Subnet | Connected Hosts | Access Policy |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Area 0 (Headquarters)** | Sales | VLAN 10 | `172.16.0.0` | PC0 – PC3 | Full Server Access |
| **Area 0 (Headquarters)** | Human Resources (HR) | VLAN 20 | `172.16.8.0` | PC4 – PC7 | Restricted (Blocked by ACL) |
| **Area 0 (Headquarters)** | IT Administration | VLAN 30 | `172.16.16.0` | PC8 – PC11 | Full Server Access |
| **Area 1 (Branch Office)** | Finance & Accounts | VLAN 40 | `172.16.24.0` | PC12 – PC14 | Internal Branch Operations |
| **Area 1 (Branch Office)** | Customer Support | VLAN 50 | `172.16.32.0` | PC15 – PC17 | Internal Branch Operations |
| **Area 2 (Data Center)** | Central Server Farm | — | `172.16.40.0` | Server 0 – 3 | Web, DNS, Storage Services |
| **Core Backbone** | Transit Link (HQ ↔ Branch) | — | `172.16.48.0` | Router 1 – Router 2 | OSPF Area 0 / 1 Transit |
| **Core Backbone** | Transit Link (Branch ↔ Data Center) | — | `172.16.56.0` | Router 2 – Router 3 | OSPF Area 1 / 2 Transit |

```text
Protocol-Stack-&-Technologies
│
├── Dynamic Routing
│   ├── Multi-Area OSPFv2   : Hierarchical routing across Area 0 (Core), Area 1, and Area 2
│   └── Router-on-a-Stick   : Layer 3 inter-VLAN routing via 802.1Q sub-interfaces
│
├── Switching & Segmentation
│   ├── IEEE 802.1Q Trunk   : Encapsulation for multi-VLAN trunk links
│   └── VLAN Architecture  : Broadcast isolation for VLANs 10, 20, 30, 40, and 50
│
├── Perimeter Security
│   ├── Ingress ACL Rules   : Access control lists filtering traffic at source interfaces
│   └── Remote Management   : Secured VTY lines for Telnet administration
│
├── Network Services
│   ├── DHCP Service        : Automated IP, mask, and gateway assignment per subnet
│   └── PAT (NAT Overload)  : Translates private subnets to external border IP addresses
│
└── Platform & Simulation
    └── Cisco Packet Tracer : Topology emulation, packet flow analysis, and ping verification
```
## Verification and Testing

Every feature was verified inside Cisco Packet Tracer to guarantee reliable packet delivery, dynamic routing convergence, and airtight access policy enforcement:

* **Multi-Area OSPF Routing:** Ran `show ip ospf neighbor` and `show ip route ospf`. Confirmed full neighbor adjacencies across all three routers, with inter-area routes (`O IA`) populating automatically across Area 0, Area 1, and Area 2.
  
* **ROAS Inter-VLAN Routing:** `show ip interface brief`. Traced hops using tracert with 0% packet loss. Also tested connectivity to the default gateway by `ping 172.16.0.1.`
  
* **DHCP Auto-Addressing:** Tested client endpoints using `ipconfig /renew`. Devices across all 5 VLANs instantly pulled correct IP leases, subnet masks, and default gateways from local router pools.
  
* **Network Security (ACL):** `ping 172.16.40.2`(from PC0-VLAN 10(SALES) which returns Reply from...(permitted).
  `ping 172.16.40.2`(from PC4-VLAN 20(HR) which returns Destination host unreachable(blocked).
  Ran `show access-lists`on router to show the packet match counter increased for ACL.
  
 * **Sales (VLAN 10) & IT (VLAN 30) ➔ Server Farm:** **[PASS]** Continuous ICMP ping requests returned `Reply from 172.16.40.2`.
  
  * **HR / Restricted (VLAN 20) ➔ Server Farm:** **[BLOCKED]** ACL dropped packets at the source gateway (`Destination host unreachable`), preventing unauthorized access and conserving bandwidth.
  
* **PAT / NAT Overload:** Verified active address translations with `show ip nat translations`. Dynamically translating multiple internal client sessions through a single border exit IP.
* **Telnet Remote Administration:** Confirmed secure administrative access to Core Router 3 via `telnet 172.16.56.2` from authorized IT management workstations with password authentication.

## Ping
* Ping from PC to SERVER.
  
<img width="792" height="757" alt="ping(N-S)" src="https://github.com/user-attachments/assets/c86e512e-a0e5-45b0-8c33-a74b64a74703" />

* Ping from SERVER to PC
  
  <img width="797" height="760" alt="ping(S to N)" src="https://github.com/user-attachments/assets/69eff2b6-583f-47e8-9ed4-81e5b75efa2a" />


