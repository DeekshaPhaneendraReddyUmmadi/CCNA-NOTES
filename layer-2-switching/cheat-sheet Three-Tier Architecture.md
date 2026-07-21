
# Layer 2 Cheat Sheet - Organized by Three-Tier Architecture
### Core -> Distribution -> Access | Technology Mapped to Layer

---

## Table of Contents
- [Layer 2 Cheat Sheet - Organized by Three-Tier Architecture](#layer-2-cheat-sheet---organized-by-three-tier-architecture)
  - [Why This Organization Matters](#why-this-organization-matters)
- [SECTION 0 - FOUNDATIONAL CONFIG (Applies to ALL Layers)](#section-0---foundational-config-applies-to-all-layers)
  - [0.1 - Base Switch Config (Every Device - Core, Dist, Access)](#01---base-switch-config-every-device---core-dist-access)
  - [0.2 - VLAN Database (Create on EVERY Switch - Core, Dist, Access)](#02---vlan-database-create-on-every-switch---core-dist-access)
  - [0.3 - VTP Role by Layer (Critical Design Decision)](#03---vtp-role-by-layer-critical-design-decision)
  - [0.4 - Management SVI (Every Layer)](#04---management-svi-every-layer)
- [SECTION 1 - CORE LAYER](#section-1---core-layer)
  - [What Belongs at the Core](#what-belongs-at-the-core)
  - [1.1 - MST Region Configuration (Core Only)](#11---mst-region-configuration-core-only)
  - [1.2 - Root Bridge Priority (Core Only)](#12---root-bridge-priority-core-only)
  - [1.3 - LACP EtherChannel (Core-to-Core)](#13---lacp-etherchannel-core-to-core)
  - [1.4 - Load Balancing (Core)](#14---load-balancing-core)
  - [1.5 - CDP/LLDP at Core (Trusted Zone - Leave Enabled)](#15---cdplldp-at-core-trusted-zone---leave-enabled)
  - [1.6 - Core Layer Verification Commands](#16---core-layer-verification-commands)
- [SECTION 2 - DISTRIBUTION LAYER](#section-2---distribution-layer)
  - [What Belongs at Distribution](#what-belongs-at-distribution)
  - [2.1 - STP Mode (Distribution)](#21---stp-mode-distribution)
  - [2.2 - PAgP EtherChannel (Distribution-to-Core)](#22---pagp-etherchannel-distribution-to-core)
  - [2.3 - Static EtherChannel (Distribution-to-Distribution)](#23---static-etherchannel-distribution-to-distribution)
  - [2.4 - Trunk VLAN Pruning (Distribution-to-Access)](#24---trunk-vlan-pruning-distribution-to-access)
  - [2.5 - Root Guard (Critical Distribution Feature)](#25---root-guard-critical-distribution-feature)
  - [2.6 - Loop Guard (Distribution)](#26---loop-guard-distribution)
  - [2.7 - Path Cost Manipulation (Distribution)](#27---path-cost-manipulation-distribution)
  - [2.8 - Distribution Layer Verification Commands](#28---distribution-layer-verification-commands)
- [SECTION 3 - ACCESS LAYER](#section-3---access-layer)
  - [What Belongs at Access](#what-belongs-at-access)
  - [3.1 - STP Mode (Access)](#31---stp-mode-access)
  - [3.2 - Access Port + Voice VLAN](#32---access-port--voice-vlan)
  - [3.3 - PortFast + BPDU Guard (Every End-Device Port)](#33---portfast--bpdu-guard-every-end-device-port)
  - [3.4 - BPDU Filter (Selective Use)](#34---bpdu-filter-selective-use)
  - [3.5 - Port Security (Sticky MAC + Violation Modes)](#35---port-security-sticky-mac--violation-modes)
  - [3.6 - DHCP Snooping (Access - Primary Enforcement Point)](#36---dhcp-snooping-access---primary-enforcement-point)
  - [3.7 - Dynamic ARP Inspection (Access)](#37---dynamic-arp-inspection-access)
  - [3.8 - IP Source Guard (Access)](#38---ip-source-guard-access)
  - [3.9 - CDP/LLDP Disabled on Edge Ports](#39---cdplldp-disabled-on-edge-ports)
  - [3.10 - Uplink Trunk to Distribution (Access Side)](#310---uplink-trunk-to-distribution-access-side)
  - [3.11 - Access Layer Verification Commands](#311---access-layer-verification-commands)
- [SECTION 4 - TECHNOLOGY-TO-LAYER MASTER MATRIX](#section-4---technology-to-layer-master-matrix)
  - [Complete Technology Placement Reference](#complete-technology-placement-reference)
  - [End-to-End Traffic Flow Example (VLAN 20 - Sales PC)](#end-to-end-traffic-flow-example-vlan-20---sales-pc)
  - [Design Philosophy Summary](#design-philosophy-summary)

---

## Why This Organization Matters

Each layer in the three-tier model has a **specific job**. The Layer 2 technologies you deploy should match that job - not be scattered randomly. This cheat sheet tells you **exactly which technology belongs at which layer** and why.

```text
┌─────────────────────────────────────────────────────┐
│                    CORE LAYER                        │
│         Job: Fast, stable, resilient backbone         │
│    Technologies: MST Root, LACP, High-speed trunking  │
└───────────────────────┬───────────────────────────────┘
                        │
┌───────────────────────┴───────────────────────────────┐
│                DISTRIBUTION LAYER                     │
│      Job: Policy enforcement, redundancy, boundary     │
│  Technologies: Rapid PVST+, Root Guard, PAgP, Pruning │
└───────────────────────┬───────────────────────────────┘
                        │
┌───────────────────────┴───────────────────────────────┐
│                   ACCESS LAYER                        │
│        Job: End-user connectivity & security           │
│  Technologies: VLANs, Port Security, DHCP Snoop, DAI  │
└─────────────────────────────────────────────────────────┘
```

---
---

# SECTION 0 - FOUNDATIONAL CONFIG (Applies to ALL Layers)

---

## 0.1 - Base Switch Config (Every Device - Core, Dist, Access)

```text
configure terminal
hostname <DEVICE-NAME>
no ip domain-lookup
enable secret Cisco!123

line console 0
 password Cisco!123
 login
 logging synchronous

line vty 0 15
 login local
 transport input ssh

username admin privilege 15 secret Cisco!123
ip domain-name enterprise.lab
crypto key generate rsa modulus 1024
ip ssh version 2

banner motd # AUTHORIZED ACCESS ONLY - Enterprise Lab Network #
end
write memory
```

---

## 0.2 - VLAN Database (Create on EVERY Switch - Core, Dist, Access)

```text
configure terminal
vlan 10
 name MGMT
vlan 20
 name DATA-SALES
vlan 30
 name DATA-ENGG
vlan 40
 name VOICE
vlan 50
 name NATIVE
vlan 99
 name BLACKHOLE
vlan 100
 name SERVER-FARM
end
```

---

## 0.3 - VTP Role by Layer (Critical Design Decision)

| Layer | VTP Mode | Why |
|---|---|---|
| **Core** | Server | Source of truth for VLAN database |
| **Distribution** | Transparent | Acts as a boundary - protects against bad VLAN updates propagating unchecked |
| **Access** | Client | Receives VLAN DB, cannot alter it - prevents accidental VLAN deletion at the edge |

```text
! Core switches
configure terminal
vtp mode server
vtp domain ENTERPRISE-L2
vtp version 2
vtp password VtpLab!2026
end

! Distribution switches
configure terminal
vtp mode transparent
vtp domain ENTERPRISE-L2
vtp version 2
vtp password VtpLab!2026
end

! Access switches
configure terminal
vtp mode client
vtp domain ENTERPRISE-L2
vtp version 2
vtp password VtpLab!2026
end
```

---

## 0.4 - Management SVI (Every Layer)

```text
configure terminal
interface vlan 10
 ip address <UNIQUE-IP> 255.255.255.0
 no shutdown

interface vlan 1
 no ip address
 shutdown

ip default-gateway 192.168.10.1     ! On all except CORE-SW1
end
```

---
---

# SECTION 1 - CORE LAYER
### *CORE-SW1, CORE-SW2 | Job: High-speed, resilient backbone*

---

## What Belongs at the Core

| Technology | Used Here? | Reason |
|---|---|---|
| MST (Multiple Spanning Tree) | Yes | Core aggregates many VLANs - MST instances reduce STP overhead |
| LACP EtherChannel | Yes | Standards-based, most resilient bundling between core switches |
| Root Bridge Election | Yes | Core MUST be the STP Root - shortest path for all traffic |
| Trunking (802.1Q) | Yes | Carries all VLANs across the backbone |
| Port Security | No | Core ports connect to trusted infrastructure, not end users |
| DHCP Snooping / DAI | No | These are edge-facing security tools - irrelevant at Core |
| PortFast / BPDU Guard | No | No end devices connect here - would be misapplied |
| Voice VLAN | No | Only relevant at Access, where phones physically connect |

---

## 1.1 - MST Region Configuration (Core Only)

```text
configure terminal

spanning-tree mst configuration
 name ENTERPRISE-CORE
 revision 1
 instance 1 vlan 10,20,50,100
 instance 2 vlan 30,40,99
 exit

spanning-tree mode mst
end
```

> **Note:** **Both Core switches must have IDENTICAL MST region config** - name, revision, instance mapping. Any mismatch creates a region boundary.

---

## 1.2 - Root Bridge Priority (Core Only)

```text
! CORE-SW1 - Primary Root Instance 1
configure terminal
spanning-tree mst 1 priority 4096
spanning-tree mst 2 priority 8192
end

! CORE-SW2 - Primary Root Instance 2
configure terminal
spanning-tree mst 2 priority 4096
spanning-tree mst 1 priority 8192
end
```

---

## 1.3 - LACP EtherChannel (Core-to-Core)

```text
! CORE-SW1 - Active
configure terminal
interface range GigabitEthernet1/0/1 - 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,30,40,50,100
 switchport nonegotiate
 channel-group 1 mode active
 no shutdown
end

! CORE-SW2 - Passive
configure terminal
interface range GigabitEthernet1/0/1 - 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,30,40,50,100
 switchport nonegotiate
 channel-group 1 mode passive
 no shutdown
end
```

---

## 1.4 - Load Balancing (Core)

```text
configure terminal
port-channel load-balance src-dst-mac
end
```

> **Note:** **Core switches carry many aggregated flows** - src-dst-mac gives better traffic distribution across bundled links than a single hash key.

---

## 1.5 - CDP/LLDP at Core (Trusted Zone - Leave Enabled)

```text
! CDP and LLDP remain enabled on all inter-switch links
! No need to disable - Core-to-Core/Distribution is a trusted zone
cdp run
lldp run
```

---

## 1.6 - Core Layer Verification Commands

```text
show spanning-tree mst
show spanning-tree mst 1
show spanning-tree mst configuration
show etherchannel summary
show lacp neighbor
show interfaces trunk
show vtp status
show cdp neighbors detail
```

---
---

# SECTION 2 - DISTRIBUTION LAYER
### *DIST-SW1, DIST-SW2 | Job: Policy boundary, redundancy, aggregation*

---

## What Belongs at Distribution

| Technology | Used Here? | Reason |
|---|---|---|
| Rapid PVST+ | Yes | Interfaces with Access layer - needs per-VLAN STP granularity |
| PAgP EtherChannel (to Core) | Yes | Redundant uplinks to Core, Cisco-native environment |
| Static EtherChannel (Dist-to-Dist) | Yes | Cross-link between distribution switches for redundancy |
| Root Guard | Yes | **Critical** - prevents Access switches from ever becoming Root |
| Loop Guard | Yes | Protects redundant links from unidirectional failures |
| VTP Transparent | Yes | Acts as VLAN propagation boundary/checkpoint |
| Trunk VLAN Pruning | Yes | Restrict which VLANs go to which Access switch |
| Port Security | No | No end devices connect directly here |
| DHCP Snooping / DAI | Optional | Some designs enable here too - but primary enforcement is at Access |
| PortFast | No | All Distribution ports are trunks/uplinks - never PortFast |

---

## 2.1 - STP Mode (Distribution)

```text
configure terminal
spanning-tree mode rapid-pvst
end
```

---

## 2.2 - PAgP EtherChannel (Distribution-to-Core)

```text
! DIST-SW1 - Auto (responds only)
configure terminal
interface range GigabitEthernet1/0/1 - 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,30,40,50,100
 channel-group 2 mode auto
 no shutdown
end
```

> **Note:** Core side uses `desirable` (initiates); Distribution side uses `auto` (responds).

---

## 2.3 - Static EtherChannel (Distribution-to-Distribution)

```text
! DIST-SW1 and DIST-SW2 - identical config, no negotiation
configure terminal
interface range GigabitEthernet1/0/5 - 6
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,30,40,50,100
 channel-group 4 mode on
 no shutdown
end
```

---

## 2.4 - Trunk VLAN Pruning (Distribution-to-Access)

```text
! DIST-SW1 downlink to ACC-SW1 (Sales zone - no VLAN 30/100 needed)
configure terminal
interface GigabitEthernet1/0/10
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,40,50
 switchport nonegotiate
 no shutdown
end
```

---

## 2.5 - Root Guard (Critical Distribution Feature)

```text
! Apply on EVERY downlink port facing Access switches
configure terminal
interface GigabitEthernet1/0/10
 spanning-tree guard root

interface GigabitEthernet1/0/11
 spanning-tree guard root
end
```

> **Note:** **This is the single most important Distribution-layer STP feature.** Without it, a misconfigured Access switch with a lower priority could hijack the Root Bridge role and destroy your carefully engineered topology.

---

## 2.6 - Loop Guard (Distribution)

```text
configure terminal
spanning-tree loopguard default
end

! Explicit on redundant cross-links
interface GigabitEthernet1/0/5
 spanning-tree guard loop
```

---

## 2.7 - Path Cost Manipulation (Distribution)

```text
! Prefer DIST-SW2 path for VLAN 30 traffic
configure terminal
interface GigabitEthernet1/0/3
 spanning-tree vlan 30 cost 4          ! Preferred path
interface GigabitEthernet1/0/4
 spanning-tree vlan 30 cost 100        ! Alternate/blocked path
end
```

---

## 2.8 - Distribution Layer Verification Commands

```text
show spanning-tree vlan 10
show spanning-tree vlan 30 detail
show spanning-tree inconsistentports
show etherchannel summary
show pagp neighbor
show interfaces trunk
show vtp status
```

---
---

# SECTION 3 - ACCESS LAYER
### *ACC-SW1 to ACC-SW4 | Job: End-user connectivity & security enforcement*

---

## What Belongs at Access

| Technology | Used Here? | Reason |
|---|---|---|
| VLAN Assignment | Yes | End devices physically connect here |
| Voice VLAN | Yes | IP Phones connect directly to access ports |
| PortFast | Yes | End-device ports need instant forwarding |
| BPDU Guard | Yes | Prevent rogue switches on user-facing ports |
| BPDU Filter | Selective | Only on specific ports needing STP exclusion |
| Port Security (Sticky MAC) | Yes | **Primary security control at this layer** |
| DHCP Snooping | Yes | **Must be enabled here** - this is where rogue DHCP servers appear |
| Dynamic ARP Inspection | Yes | Protects against ARP spoofing from end devices |
| IP Source Guard | Yes | Final enforcement - validates IP/MAC/port bindings |
| VTP Client | Yes | Receives VLAN DB - cannot create/delete VLANs |
| CDP/LLDP Disabled on Edge | Yes | Prevent info leakage to end devices |
| Root Guard | No | Not needed - Access switches should never be near Root anyway |
| MST | No | Access uses Rapid PVST+, not MST |

---

## 3.1 - STP Mode (Access)

```text
configure terminal
spanning-tree mode rapid-pvst
end
```

---

## 3.2 - Access Port + Voice VLAN

```text
configure terminal
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 20
 switchport voice vlan 40
 no shutdown
end
```

---

## 3.3 - PortFast + BPDU Guard (Every End-Device Port)

```text
configure terminal
interface range FastEthernet0/1 - 4
 spanning-tree portfast
 spanning-tree bpduguard enable
end

! Global alternative
spanning-tree portfast bpduguard default
```

---

## 3.4 - BPDU Filter (Selective Use)

```text
configure terminal
interface FastEthernet0/15
 spanning-tree bpdufilter enable
end
```

---

## 3.5 - Port Security (Sticky MAC + Violation Modes)

```text
configure terminal

! Restrict mode
interface range FastEthernet0/1 - 2
 switchport port-security
 switchport port-security maximum 2
 switchport port-security mac-address sticky
 switchport port-security violation restrict

! Shutdown mode
interface range FastEthernet0/3 - 4
 switchport port-security
 switchport port-security maximum 2
 switchport port-security mac-address sticky
 switchport port-security violation shutdown

end
```

---

## 3.6 - DHCP Snooping (Access - Primary Enforcement Point)

```text
configure terminal
ip dhcp snooping
ip dhcp snooping vlan 20,30,40
no ip dhcp snooping information option

! Trust ONLY the uplink toward Distribution
interface GigabitEthernet0/1
 ip dhcp snooping trust
end
```

---

## 3.7 - Dynamic ARP Inspection (Access)

```text
configure terminal
ip arp inspection vlan 20,30,40
ip arp inspection validate src-mac dst-mac ip

interface GigabitEthernet0/1
 ip arp inspection trust
end
```

---

## 3.8 - IP Source Guard (Access)

```text
configure terminal
interface range FastEthernet0/1 - 4
 ip verify source
end
```

---

## 3.9 - CDP/LLDP Disabled on Edge Ports

```text
configure terminal
interface range FastEthernet0/1 - 24
 no cdp enable
 no lldp transmit
 no lldp receive
end
```

---

## 3.10 - Uplink Trunk to Distribution (Access Side)

```text
configure terminal
interface GigabitEthernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,40,50
 switchport nonegotiate
 no shutdown
end
```

---

## 3.11 - Access Layer Verification Commands

```text
show port-security
show port-security interface FastEthernet0/1
show ip dhcp snooping binding
show ip arp inspection vlan 20
show ip verify source
show spanning-tree vlan 20
show interfaces status
show interfaces trunk
show vtp status
show cdp neighbors
show lldp neighbors
```

---
---

# SECTION 4 - TECHNOLOGY-TO-LAYER MASTER MATRIX

---

## Complete Technology Placement Reference

| Technology | Core | Distribution | Access |
|---|---|---|---|
| VLAN Creation | Yes (as Server) | Yes (as Transparent) | Yes (as Client) |
| VTP Mode | Server | Transparent | Client |
| STP Mode | MST | Rapid PVST+ | Rapid PVST+ |
| Root Bridge | Yes (Primary/Secondary) | Never | Never |
| EtherChannel Protocol | LACP | PAgP + Static | No (single uplinks only) |
| Native VLAN Config | Yes | Yes | Yes |
| Trunk VLAN Pruning | Full allowed list | Pruned per Access zone | N/A (receives pruned trunk) |
| PortFast | No | No | Yes |
| BPDU Guard | No | No | Yes |
| BPDU Filter | No | No | Selective |
| Root Guard | No | Yes (Critical) | No |
| Loop Guard | Optional | Yes | Optional |
| Voice VLAN | No | No | Yes |
| Port Security | No | No | Yes |
| DHCP Snooping | No | Optional | Yes (Primary) |
| DAI | No | Optional | Yes (Primary) |
| IP Source Guard | No | No | Yes |
| CDP/LLDP on Edge Ports | N/A | N/A | Disabled |
| CDP/LLDP on Uplinks | Enabled | Enabled | Enabled |

---

## End-to-End Traffic Flow Example (VLAN 20 - Sales PC)

```text
PC-S1 (VLAN 20)
   │
   ▼
ACC-SW1 Fa0/1
  [Port Security (Checked)] [DHCP Snoop Untrusted (Checked)] [DAI Check (Checked)] [IP Source Guard (Checked)]
   │
   ▼ (Trunk - Native VLAN 50, Allowed 10,20,40,50)
ACC-SW1 Gi0/1 (Uplink - DHCP/DAI Trusted)
   │
   ▼
DIST-SW1 Gi1/0/10
  [Root Guard (Checked)] [Rapid PVST+ Forwarding]
   │
   ▼ (PAgP EtherChannel Po2)
CORE-SW1
  [MST Instance 1 - Root Bridge] [LACP Po1 to CORE-SW2]
   │
   ▼
Destination (Server Farm / Internet Gateway)
```

---

## Design Philosophy Summary

```text
CORE         = SPEED + STABILITY   -> MST, LACP, Root Bridge
DISTRIBUTION = CONTROL + BOUNDARY  -> Rapid PVST+, Root Guard, PAgP, Pruning
ACCESS       = SECURITY + ACCESS   -> Port Security, DHCP Snooping, DAI, PortFast
```
