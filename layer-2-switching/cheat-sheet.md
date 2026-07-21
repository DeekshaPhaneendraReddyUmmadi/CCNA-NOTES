# Ultimate Layer 2 Switching — Master Cheat Sheet

### Index

*   [SECTION 1 — ETHERNET FUNDAMENTALS](#section-1-ethernet-fundamentals)
    *   [1.1 — Ethernet Frame Structure](#11-ethernet-frame-structure)
    *   [1.2 — MAC Address Structure](#12-mac-address-structure)
    *   [1.3 — CSMA/CD (Collision Domain)](#13-csmacd-collision-domain)
*   [SECTION 2 — SWITCH OPERATION & MAC TABLE](#section-2-switch-operation-mac-table)
    *   [2.1 — How a Switch Learns and Forwards](#21-how-a-switch-learns-and-forwards)
    *   [2.2 — MAC Address Table Commands](#22-mac-address-table-commands)
*   [SECTION 3 — VLANs](#section-3-vlans)
    *   [3.1 — VLAN Concepts](#31-vlan-concepts)
    *   [3.2 — VLAN Configuration Commands](#32-vlan-configuration-commands)
    *   [3.3 — Access Port Configuration](#33-access-port-configuration)
    *   [3.4 — Voice VLAN Configuration](#34-voice-vlan-configuration)
    *   [3.5 — VLAN Verification Commands](#35-vlan-verification-commands)
*   [SECTION 4 — TRUNKING (802.1Q)](#section-4-trunking-8021q)
    *   [4.1 — 802.1Q Frame Tagging](#41-8021q-frame-tagging)
    *   [4.2 — Trunk Port Configuration](#42-trunk-port-configuration)
    *   [4.3 — DTP Modes](#43-dtp-modes)
    *   [4.4 — Native VLAN](#44-native-vlan)
    *   [4.5 — Trunk Verification Commands](#45-trunk-verification-commands)
*   [SECTION 5 — VTP](#section-5-vtp)
    *   [5.1 — VTP Modes Comparison](#51-vtp-modes-comparison)
    *   [5.2 — VTP Configuration](#52-vtp-configuration)
    *   [5.3 — VTP Advertisement Types](#53-vtp-advertisement-types)
    *   [5.4 — VTP Revision Number Danger](#54-vtp-revision-number-danger)
    *   [5.5 — VTP Version Differences](#55-vtp-version-differences)
    *   [5.6 — VTP Verification Commands](#56-vtp-verification-commands)
*   [SECTION 6 — ETHERCHANNEL](#section-6-etherchannel)
    *   [6.1 — EtherChannel Concepts](#61-etherchannel-concepts)
    *   [6.2 — EtherChannel Protocols](#62-etherchannel-protocols)
    *   [6.3 — LACP Configuration](#63-lacp-configuration)
    *   [6.4 — PAgP Configuration](#64-pagp-configuration)
    *   [6.5 — Static EtherChannel](#65-static-etherchannel)
    *   [6.6 — EtherChannel Mode Compatibility](#66-etherchannel-mode-compatibility)
    *   [6.7 — Load Balancing](#67-load-balancing)
    *   [6.8 — EtherChannel Verification](#68-etherchannel-verification)
*   [SECTION 7 — SPANNING TREE PROTOCOL (STP)](#section-7-spanning-tree-protocol-stp)
    *   [7.1 — Why STP Exists](#71-why-stp-exists)
    *   [7.2 — STP Versions Comparison](#72-stp-versions-comparison)
    *   [7.3 — STP Root Bridge Election](#73-stp-root-bridge-election)
    *   [7.4 — STP Port Roles](#74-stp-port-roles)
    *   [7.5 — STP Port States](#75-stp-port-states)
    *   [7.6 — STP Timers](#76-stp-timers)
    *   [7.7 — STP Configuration Commands](#77-stp-configuration-commands)
    *   [7.8 — MST Configuration](#78-mst-configuration)
    *   [7.9 — STP Port Cost Reference](#79-stp-port-cost-reference)
    *   [7.10 — STP Verification Commands](#710-stp-verification-commands)
*   [SECTION 8 — STP SECURITY FEATURES](#section-8-stp-security-features)
    *   [8.1 — PortFast](#81-portfast)
    *   [8.2 — BPDU Guard](#82-bpdu-guard)
    *   [8.3 — BPDU Filter](#83-bpdu-filter)
    *   [8.4 — Root Guard](#84-root-guard)
    *   [8.5 — Loop Guard](#85-loop-guard)
    *   [8.6 — STP Security Verification](#86-stp-security-verification)
*   [SECTION 9 — LAYER 2 SECURITY](#section-9-layer-2-security)
    *   [9.1 — Port Security](#91-port-security)
    *   [9.2 — DHCP Snooping](#92-dhcp-snooping)
    *   [9.3 — Dynamic ARP Inspection (DAI)](#93-dynamic-arp-inspection-dai)
    *   [9.4 — IP Source Guard](#94-ip-source-guard)
    *   [9.5 — Layer 2 Security Verification](#95-layer-2-security-verification)
*   [SECTION 10 — CDP & LLDP](#section-10-cdp-lldp)
*   [SECTION 11 — PORT TYPES](#section-11-port-types)
*   [SECTION 12 — SVIs & L2/L3 BOUNDARY](#section-12-svis-l2l3-boundary)
*   [SECTION 13 — MASTER COMMAND REFERENCE](#section-13-master-command-reference)
*   [SECTION 14 — TROUBLESHOOTING GUIDE](#section-14-troubleshooting-guide)

***

which is best ai model is well suited for this job?

For maintaining complex, functional Markdown hierarchies with anchor-based navigation, **Claude 3.5 Sonnet** is the most reliable model. It consistently interprets header-to-slug mapping rules correctly, ensuring that internal documentation links remain functional across different rendering environments.  
 

# SECTION 1 ETHERNET FUNDAMENTALS

---

## 1.1 Ethernet Frame Structure

```
┌──────────┬──────────┬──────┬─────────────┬──────┬──────────┐
│ Preamble │   SFD    │ DST  │     SRC     │Type/ │  FCS     │
│  7 bytes │ 1 byte   │MAC   │     MAC     │Len   │ 4 bytes  │
│          │          │6bytes│   6 bytes   │2bytes|          │
└──────────┴──────────┴──────┴─────────────┴──────┴──────────┘
```

| Field | Size | Purpose |
|---|---|---|
| Preamble | 7 bytes | Synchronization — alerts receiving NIC |
| SFD (Start Frame Delimiter) | 1 byte | Marks end of preamble, start of frame |
| Destination MAC | 6 bytes | Who should receive this frame |
| Source MAC | 6 bytes | Who sent this frame |
| Type/Length | 2 bytes | Protocol type (IPv4=0x0800, ARP=0x0806) |
| Data/Payload | 46-1500 bytes | Actual data being carried |
| FCS | 4 bytes | Error detection (CRC check) |

> **Minimum frame size = 64 bytes. Maximum = 1518 bytes (1522 with 802.1Q tag).** Frames smaller than 64 bytes are called **runts** and are discarded.

---

## 1.2 MAC Address Structure

```
  OUI (Vendor ID)          Device ID
┌──────────────────┬──────────────────────┐
│  AA : BB : CC    │    DD : EE : FF      │
│   3 bytes        │      3 bytes         │
│ Assigned by IEEE │ Assigned by vendor   │
└──────────────────┴──────────────────────┘
```

### MAC Address Types

| Type | Example | Description |
|---|---|---|
| **Unicast** | 00:1A:2B:3C:4D:5E | One specific device |
| **Broadcast** | FF:FF:FF:FF:FF:FF | All devices on segment |
| **Multicast** | 01:00:5E:xx:xx:xx | Group of devices |
| **Locally Administered** | Second bit of first byte = 1 | Manually assigned |
| **Universally Administered** | Second bit of first byte = 0 | Burned-in from factory |

---

## 1.3 CSMA/CD (Collision Domain)

```
Hub Environment (CSMA/CD Active)
┌────┐     ┌────┐     ┌────┐
│PC-A│─────│HUB │─────│PC-B│
└────┘     └────┘     └────┘
     All devices share ONE collision domain
     
Switch Environment (No CSMA/CD needed)
┌────┐     ┌────────┐     ┌────┐
│PC-A│─────│SWITCH  │─────│PC-B│
└────┘     │(buffer)│     └────┘
           └────────┘
     Each port = its own collision domain
     Full duplex = CSMA/CD disabled
```

| Feature | Hub | Switch |
|---|---|---|
| Collision Domain | One shared | Per port |
| Broadcast Domain | One shared | One shared (per VLAN) |
| Duplex | Half only | Full duplex |
| CSMA/CD | Required | Not needed |
| MAC Learning | No | Yes |

---
---

# SECTION 2 SWITCH OPERATION & MAC TABLE

---

## 2.1 How a Switch Learns and Forwards

```
Step 1 — LEARN:
Frame arrives → Switch reads SOURCE MAC
→ Records {Source MAC : Incoming Port : VLAN} in MAC table
→ Starts aging timer (default 300 seconds)

Step 2 — DECIDE:
Switch looks up DESTINATION MAC in MAC table

Step 3 — ACTION:
┌──────────────────────────────────────────────────┐
│ Known Unicast    → Forward out SPECIFIC port     │
│ Unknown Unicast  → FLOOD out ALL ports (same VLAN│
│                    except incoming port)         │
│ Broadcast        → FLOOD out ALL ports (same VLAN│
│                    except incoming port)         │
│ Multicast        → FLOOD (unless IGMP snooping)  │
└──────────────────────────────────────────────────┘
```

---

## 2.2 MAC Address Table Commands

```
! View full MAC table
show mac address-table

! View MAC table for specific VLAN
show mac address-table vlan 20

! View MAC table for specific MAC
show mac address-table address 00d0.ba48.4e01

! View dynamic entries only
show mac address-table dynamic

! View static entries only
show mac address-table static

! View MAC table count
show mac address-table count

! Clear dynamic MAC entries
clear mac address-table dynamic

! Clear for specific VLAN
clear mac address-table dynamic vlan 20

! Clear for specific interface
clear mac address-table dynamic interface Fa0/1

! Change aging timer (default 300 sec)
configure terminal
mac address-table aging-time 600
end

! Add a static MAC entry
configure terminal
mac address-table static 0050.7966.6800 vlan 20 interface Fa0/3
end
```

---
---

# SECTION 3 VLANs (Virtual Local Area Networks)

---

## 3.1 VLAN Concepts

```
Without VLANs — One Broadcast Domain
┌──────────────────────────────────────┐
│  PC1   PC2   PC3   PC4   PC5   PC6   │
│  All receive ALL broadcasts          │
└──────────────────────────────────────┘

With VLANs — Segmented Broadcast Domains
┌─────────────────┐    ┌─────────────────┐
│    VLAN 10      │    │    VLAN 20      │
│  PC1  PC2  PC3  │    │  PC4  PC5  PC6  │
│  Own broadcast  │    │  Own broadcast  │
│  domain         │    │  domain         │
└─────────────────┘    └─────────────────┘
```

### VLAN Ranges Reference

| Range | VLAN IDs | Purpose |
|---|---|---|
| **Normal Range** | 1 — 1005 | Standard use — stored in vlan.dat |
| **Extended Range** | 1006 — 4094 | Requires VTP Transparent or VTPv3 |
| **Reserved** | 1002 — 1005 | FDDI and Token Ring (do not use) |
| **Default VLAN** | 1 | Cannot be deleted or renamed |
| **Internal Use** | 1006 — 4094 | IOS uses some internally |

---

## 3.2 VLAN Configuration Commands

```
! Create VLAN
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

! Delete a VLAN
configure terminal
no vlan 30
end

! Verify
show vlan brief
show vlan id 10
show vlan name MGMT
```

---

## 3.3 Access Port Configuration

```
configure terminal

interface FastEthernet0/1
 switchport mode access
 switchport access vlan 20
 no shutdown
end

! Verify
show interfaces FastEthernet0/1 switchport
show interfaces FastEthernet0/1 status
```

---

## 3.4 Voice VLAN Configuration

```
configure terminal

interface FastEthernet0/5
 switchport mode access
 switchport access vlan 20        ! Data VLAN for PC
 switchport voice vlan 40         ! Voice VLAN for Phone
 no shutdown
end
```

> **How voice VLAN works on a single port:**
> ```
> Switch Port Fa0/5
>       │
>       ▼
>   IP Phone ──── tagged VLAN 40 ──── to switch
>       │
>       ▼
>     PC ──────── untagged VLAN 20 ── to switch
> ```
> The phone tags its own traffic with VLAN 40. The PC connects to the phone's passthrough port — its traffic is untagged (VLAN 20).

---

## 3.5 VLAN Verification Commands

```
show vlan brief
show vlan id 20
show vlan summary
show interfaces FastEthernet0/1 switchport
show interfaces status
show mac address-table vlan 20
```

---
---

# SECTION 4 TRUNKING (802.1Q)

---

## 4.1 802.1Q Frame Tagging

```
Normal Ethernet Frame:
┌──────────┬──────────┬──────┬─────┬─────┐
│   DST    │   SRC    │ Type │Data │ FCS │
│   MAC    │   MAC    │      │     │     │
└──────────┴──────────┴──────┴─────┴─────┘

802.1Q Tagged Frame (4 bytes inserted):
┌──────────┬──────────┬──────────────────┬──────┬─────┬─────┐
│   DST    │   SRC    │   802.1Q TAG     │ Type │Data │ FCS │
│   MAC    │   MAC    │ 0x8100│PRI│VLAN# │      │     │     │
└──────────┴──────────┴──────────────────┴──────┴─────┴─────┘
                       └─── 4 bytes ────┘
                       TPID    PCP  DEI  VID
                       2bytes  3bit 1bit 12bit
```

| 802.1Q Tag Field | Size | Purpose |
|---|---|---|
| TPID (Tag Protocol ID) | 16 bits | Always 0x8100 — identifies 802.1Q frame |
| PCP (Priority Code Point) | 3 bits | CoS — Class of Service (0-7) |
| DEI (Drop Eligible) | 1 bit | Drop eligibility during congestion |
| VID (VLAN ID) | 12 bits | VLAN number (0-4095) |

---

## 4.2 Trunk Port Configuration

```
configure terminal

interface GigabitEthernet1/0/1

 ! Required on 3650 — specify encapsulation first
 switchport trunk encapsulation dot1q

 ! Force trunk mode — never auto
 switchport mode trunk

 ! Disable DTP negotiation
 switchport nonegotiate

 ! Set native VLAN (NOT VLAN 1)
 switchport trunk native vlan 50

 ! Prune allowed VLANs
 switchport trunk allowed vlan 10,20,30,40,50,100

 no shutdown
end
```

---

## 4.3 DTP (Dynamic Trunking Protocol) Modes

| Mode | Behavior | Becomes Trunk If... |
|---|---|---|
| `dynamic auto` | Passively waits | Other side is desirable or trunk |
| `dynamic desirable` | Actively negotiates | Other side is auto, desirable, or trunk |
| `trunk` | Always trunk | Always — regardless of other side |
| `access` | Always access | Always — regardless of other side |
| `nonegotiate` | No DTP frames sent | Must be combined with trunk or access |

### DTP Combinations — Will It Trunk?

| Side A | Side B | Result |
|---|---|---|
| trunk | trunk | ✅ Trunk |
| trunk | dynamic auto | ✅ Trunk |
| trunk | dynamic desirable | ✅ Trunk |
| dynamic desirable | dynamic desirable | ✅ Trunk |
| dynamic desirable | dynamic auto | ✅ Trunk |
| dynamic auto | dynamic auto | ❌ Access |
| access | trunk | ❌ Access (mismatch) |

---

## 4.4 Native VLAN

```
! Native VLAN frames are sent UNTAGGED on trunk links
! Default native VLAN = VLAN 1 (security risk)

! Change native VLAN on trunk
configure terminal
interface GigabitEthernet1/0/1
 switchport trunk native vlan 50
end

! Check for native VLAN mismatches
show interfaces trunk
! Look for "Native VLAN" column

! CDP will warn about mismatch:
! %CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch on port
```

> **Native VLAN must match on BOTH ends of a trunk.** A mismatch causes VLAN leaking — traffic from one VLAN can bleed into another. This is the basis of a **VLAN hopping attack**.

---

## 4.5 Trunk Verification Commands

```
show interfaces trunk
show interfaces GigabitEthernet1/0/1 switchport
show interfaces GigabitEthernet1/0/1 trunk
show dtp interface GigabitEthernet1/0/1
show vlan brief
```

### Reading `show interfaces trunk` Output

```
Port      Mode         Encapsulation  Status        Native vlan
Gi1/0/1   on           802.1q         trunking      50        ← ✅ correct

Port      Vlans allowed on trunk
Gi1/0/1   10,20,30,40,50,100

Port      Vlans allowed and active in management domain
Gi1/0/1   10,20,30,40,50,100

Port      Vlans in spanning tree forwarding state and not pruned
Gi1/0/1   10,20,30,40,50,100
```

---
---

# SECTION 5 VTP (VLAN Trunking Protocol)

---

## 5.1 VTP Modes Comparison

| Feature | Server | Client | Transparent | Off (VTPv3) |
|---|---|---|---|---|
| Create VLANs | ✅ Yes | ❌ No | ✅ Local only | ✅ Local only |
| Delete VLANs | ✅ Yes | ❌ No | ✅ Local only | ✅ Local only |
| Sends VTP Ads | ✅ Yes | ✅ Yes | ✅ Forwards only | ❌ No |
| Receives/Applies | ✅ Yes | ✅ Yes | ❌ Ignores | ❌ Ignores |
| Saves to NVRAM | ✅ Yes | ❌ No | ✅ Yes | ✅ Yes |
| Revision Number | Increments | Tracks server | Always 0 | Always 0 |

---

## 5.2 VTP Configuration

```
! VTP Server
configure terminal
vtp mode server
vtp domain ENTERPRISE-L2
vtp version 2
vtp password VtpLab!2026
end

! VTP Client
configure terminal
vtp mode client
vtp domain ENTERPRISE-L2
vtp version 2
vtp password VtpLab!2026
end

! VTP Transparent
configure terminal
vtp mode transparent
vtp domain ENTERPRISE-L2
vtp version 2
vtp password VtpLab!2026
end
```

---

## 5.3 VTP Advertisement Types

| Type | Direction | Purpose |
|---|---|---|
| **Summary** | Server → all | Sent every 5 min or on change — contains domain, revision |
| **Subset** | Server → all | Contains actual VLAN details after a change |
| **Request** | Client → Server | Client asking for VLAN database update |

---

## 5.4 VTP Revision Number Danger

```
DANGER SCENARIO:
Old switch (revision 250) joins your network
Your VTP Server revision = 100
Result: OLD switch OVERWRITES your VLAN database!

FIX BEFORE CONNECTING ANY UNKNOWN SWITCH:
Method 1 — Reset via Transparent mode:
  conf t → vtp mode transparent → vtp mode server/client
  (revision resets to 0)

Method 2 — Change domain name:
  conf t → vtp domain TEMP → vtp domain ENTERPRISE-L2
  (revision resets to 0)
```

---

## 5.5 VTP Version Differences

| Feature | VTPv1 | VTPv2 | VTPv3 |
|---|---|---|---|
| Extended VLANs | ❌ | ❌ | ✅ |
| Primary Server concept | ❌ | ❌ | ✅ |
| MST propagation | ❌ | ❌ | ✅ |
| Database protection | ❌ | ❌ | ✅ |
| Per-port basis | ❌ | ❌ | ✅ |
| Hidden password | ❌ | ❌ | ✅ |

---

## 5.6 VTP Verification Commands

```
show vtp status
show vtp counters
show vtp password
show running-config | section vtp
```

---
---

# SECTION 6 ETHERCHANNEL (Link Aggregation)

---

## 6.1 — EtherChannel Concepts

```
Without EtherChannel:
SW1 ──Gi0/1──► SW2   (1 Gbps, STP blocks redundant links)
SW1 ──Gi0/2──► SW2   (BLOCKED by STP — wasted bandwidth)

With EtherChannel (Po1):
SW1 ══════════► SW2   (2 Gbps logical bundle)
    Gi0/1+Gi0/2       (Both active, STP sees ONE link)
```

---

## 6.2 EtherChannel Protocols

| Feature | LACP | PAgP | Static (On) |
|---|---|---|---|
| Standard | IEEE 802.3ad | Cisco Proprietary | None |
| Modes | active / passive | desirable / auto | on |
| Negotiation | Yes | Yes | No |
| Max ports | 16 (8 active) | 8 | 8 |
| Cross-vendor | ✅ Yes | ❌ Cisco only | ✅ Yes |
| Detects mismatch | ✅ Yes | ✅ Yes | ❌ No |

---

## 6.3 LACP Configuration

```
! Active side (initiates)
configure terminal
interface range GigabitEthernet1/0/1 - 2
 switchport mode trunk
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,30,40,50,100
 channel-group 1 mode active
 no shutdown
end

! Passive side (responds)
configure terminal
interface range GigabitEthernet1/0/1 - 2
 switchport mode trunk
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,30,40,50,100
 channel-group 1 mode passive
 no shutdown
end

! Configure Port-Channel interface
interface Port-channel1
 switchport mode trunk
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,30,40,50,100
end
```

---

## 6.4 PAgP Configuration

```
! Desirable side (initiates)
configure terminal
interface range GigabitEthernet1/0/3 - 4
 channel-group 2 mode desirable
 no shutdown
end

! Auto side (responds)
configure terminal
interface range GigabitEthernet1/0/3 - 4
 channel-group 2 mode auto
 no shutdown
end
```

---

## 6.5 Static EtherChannel

```
configure terminal
interface range GigabitEthernet1/0/5 - 6
 channel-group 4 mode on
 no shutdown
end
```

---

## 6.6 EtherChannel Mode Compatibility

| SW-A Mode | SW-B Mode | LACP Forms? | PAgP Forms? |
|---|---|---|---|
| active | active | ✅ Yes | — |
| active | passive | ✅ Yes | — |
| passive | passive | ❌ No | — |
| desirable | desirable | — | ✅ Yes |
| desirable | auto | — | ✅ Yes |
| auto | auto | — | ❌ No |
| on | on | ✅ Yes | ✅ Yes |
| on | active/passive | ❌ No | ❌ No |

---

## 6.7 Load Balancing

```
! Set load balancing method
configure terminal
port-channel load-balance src-dst-mac    ! Core/Distribution
port-channel load-balance src-mac        ! Access
port-channel load-balance src-dst-ip     ! Layer 3 environments
end

! Verify
show etherchannel load-balance
show port-channel load-balance
```

### Load Balancing Methods

| Method | CLI Keyword | Best For |
|---|---|---|
| Source MAC | `src-mac` | Access layer |
| Destination MAC | `dst-mac` | Rarely used |
| Src+Dst MAC | `src-dst-mac` | Distribution/Core |
| Source IP | `src-ip` | Routed networks |
| Destination IP | `dst-ip` | Rarely used |
| Src+Dst IP | `src-dst-ip` | Routed Core |
| Source Port | `src-port` | Advanced load balancing |

---

## 6.8 EtherChannel Verification

```
show etherchannel summary
show etherchannel port-channel
show etherchannel detail
show interfaces port-channel 1
show lacp neighbor
show lacp counters
show pagp neighbor
show pagp counters
show port-channel load-balance
```

### EtherChannel Status Flags

```
show etherchannel summary output:

Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------------
1      Po1(SU)       LACP        Gi1/0/1(P)   Gi1/0/2(P)

Flag Legend:
D - down
P - bundled in port-channel          ✅ healthy
I - stand-alone (not bundled)        ⚠️ problem
s - suspended                        ⚠️ problem
H - Hot-standby (LACP only)
R - Layer 3 port channel
S - Layer 2 port channel
U - in use                           ✅ healthy
f - failed to allocate aggregator
```

---
---

# SECTION 7 SPANNING TREE PROTOCOL (STP)

---

## 7.1 Why STP Exists

```
Without STP — Broadcast Storm:
                    ┌────────┐
          ┌────────►│  SW-B  │◄────────┐
          │         └────────┘         │
          │              │             │
          │    Broadcast loops FOREVER │
          │              ▼             │
       ┌──┴─────┐   ┌────────┐    ┌────┴───┐
       │  SW-A  │───│  SW-C  │────│  SW-D  │
       └────────┘   └────────┘    └────────┘
       
With STP — One path active, redundant path blocked:
       SW-A ──────► SW-B ──────► SW-C
                              (SW-D port blocked)
```

---

## 7.2 STP Versions Comparison

| Version | Standard | Convergence | Per-VLAN? | Notes |
|---|---|---|---|---|
| **STP** (802.1D) | IEEE | 30-50 sec | ❌ No (CST) | Original — obsolete |
| **PVST+** | Cisco | 30-50 sec | ✅ Yes | Per-VLAN STP — Cisco default |
| **RSTP** (802.1w) | IEEE | 1-6 sec | ❌ No | Rapid convergence |
| **Rapid PVST+** | Cisco | 1-6 sec | ✅ Yes | Cisco rapid per-VLAN |
| **MST** (802.1s) | IEEE | 1-6 sec | ✅ (instances) | Maps VLANs to instances |

---

## 7.3 STP Root Bridge Election

```
Election Process:
Step 1: All switches claim to be Root (send BPDUs with own Bridge ID)
Step 2: Switch with LOWEST Bridge ID wins

Bridge ID = Priority (16 bits) + MAC Address (48 bits)
         = [4-bit priority][12-bit VLAN/instance] + MAC

Default Priority = 32768
Extended System ID = VLAN number added automatically

Example:
SW1: Priority 32768 + VLAN 1 = 32769, MAC 0001.0001.0001
SW2: Priority 32768 + VLAN 1 = 32769, MAC 0002.0002.0002

SW1 wins (lower MAC as tiebreaker)

To make SW1 root intentionally:
spanning-tree vlan 10 priority 4096
  OR
spanning-tree vlan 10 root primary    (sets to 24576 or 4096)
```

---

## 7.4 STP Port Roles

```
                    [ROOT BRIDGE]
                    CORE-SW1
                   /           \
          Root Port              Root Port
              /                       \
         DIST-SW1                 DIST-SW2
        /        \               /        \
  Designated   Designated  Designated   Designated
     Port          Port       Port          Port
      /               \       /               \
  ACC-SW1          ACC-SW2 ACC-SW3          ACC-SW4
       \                              /
        Alternate Port ──────────────
        (Blocked — not best path to root)
```

| Role | Description | Forwards? |
|---|---|---|
| **Root Port (RP)** | Best path toward Root Bridge — one per switch | ✅ Yes |
| **Designated Port (DP)** | Best port on a segment toward Root — one per segment | ✅ Yes |
| **Alternate Port (AP)** | Backup path to Root (Rapid PVST+) | ❌ No |
| **Backup Port (BP)** | Backup to Designated port on same segment | ❌ No |
| **Disabled** | Administratively shut down | ❌ No |

---

## 7.5 STP Port States

### Classic STP (802.1D)

| State | Duration | Sends BPDUs | Learns MAC | Forwards |
|---|---|---|---|---|
| **Blocking** | 20 sec (Max Age) | ❌ | ❌ | ❌ |
| **Listening** | 15 sec (Forward Delay) | ✅ | ❌ | ❌ |
| **Learning** | 15 sec (Forward Delay) | ✅ | ✅ | ❌ |
| **Forwarding** | Until topology change | ✅ | ✅ | ✅ |
| **Disabled** | Admin down | ❌ | ❌ | ❌ |

### Rapid PVST+ (802.1w) — Simplified

| State | Description |
|---|---|
| **Discarding** | Replaces Blocking+Listening — port silent |
| **Learning** | Building MAC table |
| **Forwarding** | Fully active |

---

## 7.6 STP Timers

| Timer | Default | Purpose |
|---|---|---|
| **Hello Time** | 2 seconds | How often Root sends BPDUs |
| **Forward Delay** | 15 seconds | Time spent in Listening + Learning |
| **Max Age** | 20 seconds | How long to keep BPDU info before discarding |
| **Total Convergence** | 50 seconds | 20 (Max Age) + 15 (Listening) + 15 (Learning) |

```
! Modify timers (on Root Bridge only — best practice)
configure terminal
spanning-tree vlan 10 hello-time 1
spanning-tree vlan 10 forward-time 10
spanning-tree vlan 10 max-age 14
end
```

---

## 7.7 STP Configuration Commands

```
! Set STP mode
configure terminal
spanning-tree mode rapid-pvst        ! Rapid PVST+
spanning-tree mode pvst              ! Classic PVST+
spanning-tree mode mst               ! MST
end

! Set Root Bridge — Method 1 (macro)
configure terminal
spanning-tree vlan 10,20 root primary
spanning-tree vlan 30,40 root secondary
end

! Set Root Bridge — Method 2 (manual priority)
configure terminal
spanning-tree vlan 10 priority 4096     ! Primary root
spanning-tree vlan 10 priority 8192     ! Secondary root
end

! Manipulate port cost
configure terminal
interface GigabitEthernet0/1
 spanning-tree vlan 30 cost 4
end

! Manipulate port priority
configure terminal
interface GigabitEthernet0/1
 spanning-tree vlan 10 port-priority 64
end
```

---

## 7.8 MST Configuration

```
configure terminal

! Enter MST config submode
spanning-tree mst configuration
 name ENTERPRISE-CORE
 revision 1
 instance 1 vlan 10,20,50,100
 instance 2 vlan 30,40,99
 exit

! Set MST mode
spanning-tree mode mst

! Set root for MST instances
spanning-tree mst 1 priority 4096
spanning-tree mst 2 priority 8192

end
```

> **MST Instance 0 = IST (Internal Spanning Tree).** All VLANs not explicitly mapped to an instance fall into Instance 0. It also handles communication with other MST regions and with Rapid PVST+ boundaries.

---

## 7.9 STP Port Cost Reference

| Speed | 802.1D Cost | 802.1w Cost |
|---|---|---|
| 10 Mbps | 100 | 2,000,000 |
| 100 Mbps | 19 | 200,000 |
| 1 Gbps | 4 | 20,000 |
| 10 Gbps | 2 | 2,000 |
| EtherChannel 2x1G | 3 | 10,000 |

---

## 7.10 STP Verification Commands

```
show spanning-tree
show spanning-tree vlan 10
show spanning-tree vlan 10 detail
show spanning-tree summary
show spanning-tree summary totals
show spanning-tree mst
show spanning-tree mst 1
show spanning-tree mst configuration
show spanning-tree mst detail
show spanning-tree active
show spanning-tree inconsistentports
show spanning-tree blockedports
```

---
---

# SECTION 8 STP SECURITY FEATURES

---

## 8.1 PortFast

```
! Per interface
configure terminal
interface FastEthernet0/1
 spanning-tree portfast
end

! Global (all access ports)
configure terminal
spanning-tree portfast default
end

! On trunk port (rare — use with care)
configure terminal
interface GigabitEthernet0/1
 spanning-tree portfast trunk
end
```

> **PortFast skips Listening and Learning states** — port goes directly to Forwarding. Only use on ports connected to end devices (PCs, servers, printers). NEVER on switch-to-switch links.

---

## 8.2 BPDU Guard

```
! Global (applies to all PortFast-enabled ports)
configure terminal
spanning-tree portfast bpduguard default
end

! Per interface
configure terminal
interface FastEthernet0/1
 spanning-tree bpduguard enable
end

! Disable BPDU Guard on specific port
configure terminal
interface FastEthernet0/1
 spanning-tree bpduguard disable
end

! Recover from err-disable
configure terminal
interface FastEthernet0/1
 shutdown
 no shutdown
end

! Auto-recovery
configure terminal
errdisable recovery cause bpduguard
errdisable recovery interval 300
end

! Check err-disabled ports
show errdisable recovery
show interfaces FastEthernet0/1 status
show spanning-tree summary
```

---

## 8.3 BPDU Filter

```
! Per interface (completely suppresses BPDUs)
configure terminal
interface FastEthernet0/1
 spanning-tree bpdufilter enable
end

! Global (applies to PortFast ports — sends few BPDUs on init)
configure terminal
spanning-tree portfast bpdufilter default
end
```

### BPDU Guard vs BPDU Filter Full Comparison

| Behavior | BPDU Guard | BPDU Filter |
|---|---|---|
| Receives BPDU | Err-disables port | Ignores (drops) BPDU |
| Sends BPDUs | Continues normally | Stops completely |
| Port recovery | Manual / auto-recovery | Automatic (no err-disable) |
| Risk level | Low — very safe | Medium — can hide loops |
| Per-interface effect | Immediate shutdown | Transparent to STP |
| Global effect | Only on PortFast ports | Sends 11 BPDUs at startup then stops |
| Use case | User-facing ports | Provider handoff / STP domain edge |

---

## 8.4 Root Guard

```
! Apply on ports that should NEVER become Root ports
! (Distribution downlinks toward Access)
configure terminal
interface GigabitEthernet1/0/10
 spanning-tree guard root
end

! Verify
show spanning-tree inconsistentports
show spanning-tree vlan 10 detail
```

### Root Guard vs BPDU Guard

| Feature | Root Guard | BPDU Guard |
|---|---|---|
| Trigger | Superior BPDU received | ANY BPDU received |
| Port action | Root-inconsistent (discarding) | Err-disabled |
| Auto recovery | ✅ Yes — when superior BPDUs stop | ❌ No — manual required |
| Applied on | **Designated ports** (downlinks) | **PortFast/edge ports** |
| Purpose | Prevent wrong Root election | Prevent rogue switches |

---

## 8.5 Loop Guard

```
! Global
configure terminal
spanning-tree loopguard default
end

! Per interface
configure terminal
interface GigabitEthernet1/0/5
 spanning-tree guard loop
end

! Verify
show spanning-tree inconsistentports
show spanning-tree detail | include loop
```

### Loop Guard vs Other Guards

| Feature | Loop Guard | Root Guard | BPDU Guard |
|---|---|---|---|
| Protects against | Unidirectional link failure | Wrong Root election | Rogue switch on edge |
| Trigger | BPDUs stop arriving on non-designated port | Superior BPDU arrives | Any BPDU arrives |
| Port action | Loop-inconsistent | Root-inconsistent | Err-disabled |
| Auto recovery | ✅ Yes | ✅ Yes | ❌ No |
| Applied on | Non-designated/Root ports | Designated ports | PortFast ports |

---

## 8.6 STP Security Verification

```
show spanning-tree summary
show spanning-tree detail
show spanning-tree inconsistentports
show errdisable recovery
show errdisable detect
show interfaces status err-disabled
show running-config | include bpduguard
show running-config | include portfast
show running-config | include guard
```

---
---

# SECTION 9 LAYER 2 SECURITY

---

## 9.1 Port Security

```
! Full port security config
configure terminal
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 20

 ! Enable port security
 switchport port-security

 ! Set maximum MACs allowed
 switchport port-security maximum 2

 ! Sticky learning (dynamic + permanent)
 switchport port-security mac-address sticky

 ! Manually add a static MAC (optional)
 switchport port-security mac-address 0050.7966.6800

 ! Set violation mode
 switchport port-security violation shutdown
 no shutdown
end
```

### Violation Modes Complete Reference

| Mode | Drop Frame | Syslog/Trap | Increment Counter | Err-Disable Port |
|---|---|---|---|---|
| **protect** | ✅ | ❌ | ❌ | ❌ |
| **restrict** | ✅ | ✅ | ✅ | ❌ |
| **shutdown** | ✅ | ✅ | ✅ | ✅ |

```
! Recover from port security shutdown
configure terminal
interface FastEthernet0/1
 shutdown
 no shutdown
end

! Auto recovery
configure terminal
errdisable recovery cause psecure-violation
errdisable recovery interval 300
end
```

---

## 9.2 DHCP Snooping

```
! Enable globally
configure terminal
ip dhcp snooping

! Enable for specific VLANs
ip dhcp snooping vlan 20,30,40

! Disable Option 82 (important in PT)
no ip dhcp snooping information option

! Set uplink as trusted
interface GigabitEthernet0/1
 ip dhcp snooping trust

! Set rate limit on untrusted ports (optional)
interface FastEthernet0/1
 ip dhcp snooping limit rate 15

end
```

> 🔑 **DHCP Snooping Trust Model:**
> ```
> ✅ Trusted ports  → Can receive DHCP OFFER, ACK (server messages)
> ❌ Untrusted ports → Can only send DHCP DISCOVER, REQUEST (client messages)
>                      OFFER and ACK from untrusted = DROPPED
> ```

---

## 9.3 Dynamic ARP Inspection (DAI)

```
! Enable DAI for VLANs
configure terminal
ip arp inspection vlan 20,30,40

! Trust the uplink
interface GigabitEthernet0/1
 ip arp inspection trust

! Enable extended validation
ip arp inspection validate src-mac
ip arp inspection validate dst-mac
ip arp inspection validate ip

! All three at once
ip arp inspection validate src-mac dst-mac ip

! Set rate limit on untrusted ports (optional)
interface FastEthernet0/1
 ip arp inspection limit rate 100

end
```

> 🔑 **DAI ↔ DHCP Snooping binding table:**
> ```
> DHCP Snooping builds:
> {IP Address : MAC Address : VLAN : Interface : Lease Time}
>
> DAI checks incoming ARP:
> "Does ARP sender IP match the MAC in DHCP Snooping table?"
> ✅ Match  → ARP forwarded
> ❌ No match → ARP dropped (possible ARP spoofing attack)
> ```

---

## 9.4 IP Source Guard

```
! Enable per interface (DHCP Snooping must be working first)
configure terminal
interface FastEthernet0/1
 ip verify source

! With port security integration
interface FastEthernet0/1
 ip verify source port-security

end
```

> **IP Source Guard dependency chain:**
> ```
> DHCP Snooping → ON
>       ↓
> Client gets IP via DHCP
>       ↓
> Binding entry created:
> {192.168.20.10 : MAC : VLAN 20 : Fa0/1}
>       ↓
> IP Source Guard enabled
>       ↓
> Only allows traffic matching binding entry
> Any other source IP from Fa0/1 → DROPPED
> ```

---

## 9.5 Layer 2 Security Verification

```
! Port Security
show port-security
show port-security interface FastEthernet0/1
show port-security address
show port-security interface FastEthernet0/1 address

! DHCP Snooping
show ip dhcp snooping
show ip dhcp snooping binding
show ip dhcp snooping statistics
show ip dhcp snooping database

! DAI
show ip arp inspection
show ip arp inspection vlan 20
show ip arp inspection interfaces
show ip arp inspection statistics vlan 20

! IP Source Guard
show ip verify source
show ip verify source interface FastEthernet0/1
```

---
---

# SECTION 10 CDP & LLDP

---

## 10.1 CDP (Cisco Discovery Protocol)

```
! Enable globally (default ON)
configure terminal
cdp run
end

! Disable globally
configure terminal
no cdp run
end

! Disable on specific interface (edge ports)
configure terminal
interface FastEthernet0/1
 no cdp enable
end

! Re-enable on interface
configure terminal
interface FastEthernet0/1
 cdp enable
end

! Modify CDP timers
configure terminal
cdp timer 30           ! Hello every 30 sec (default 60)
cdp holdtime 120       ! Hold time (default 180)
end
```

---

## 10.2 LLDP (Link Layer Discovery Protocol)

```
! Enable globally (default OFF on IOS)
configure terminal
lldp run
end

! Disable globally
configure terminal
no lldp run
end

! Disable transmit AND receive on edge ports
configure terminal
interface FastEthernet0/1
 no lldp transmit
 no lldp receive
end

! Disable only transmit
configure terminal
interface FastEthernet0/1
 no lldp transmit
end

! Modify LLDP timers
configure terminal
lldp timer 15          ! Hello every 15 sec (default 30)
lldp holdtime 60       ! Hold time (default 120)
lldp reinit 3          ! Reinit delay (default 2)
end
```

---

## 10.3 CDP vs LLDP Comparison

| Feature | CDP | LLDP |
|---|---|---|
| Standard | Cisco Proprietary | IEEE 802.1AB |
| Default state | **Enabled** | **Disabled** |
| Multi-vendor | ❌ Cisco only | ✅ Any vendor |
| Hello timer | 60 seconds | 30 seconds |
| Hold time | 180 seconds | 120 seconds |
| Layer | Layer 2 | Layer 2 |
| VoIP info | ✅ Yes | ✅ Yes (LLDP-MED) |
| PoE info | ✅ Yes | ✅ Yes (LLDP-MED) |
| VLAN info | ✅ Yes | ✅ Yes |
| Use when | Pure Cisco | Multi-vendor / MSP |

---

## 10.4 CDP & LLDP Verification

```
! CDP
show cdp
show cdp neighbors
show cdp neighbors detail
show cdp interface
show cdp interface FastEthernet0/1
show cdp entry *
show cdp traffic

! LLDP
show lldp
show lldp neighbors
show lldp neighbors detail
show lldp interface
show lldp interface FastEthernet0/1
show lldp entry *
show lldp traffic
```

---
---

# SECTION 11 PORT TYPES & SWITCHPORT MODES

---

## 11.1 Switchport Mode Summary

```
! Access Port
configure terminal
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 20
end

! Trunk Port
configure terminal
interface GigabitEthernet1/0/1
 switchport trunk encapsulation dot1q   ! Required on 3650
 switchport mode trunk
 switchport trunk native vlan 50
 switchport trunk allowed vlan 10,20,30
 switchport nonegotiate
end

! Routed Port (Layer 3 — disable switchport)
configure terminal
interface GigabitEthernet1/0/24
 no switchport
 ip address 10.0.0.1 255.255.255.252
end

! Reset port to default
configure terminal
interface FastEthernet0/1
 default interface FastEthernet0/1
end
```

---

## 11.2 Interface Status Reference

```
show interfaces FastEthernet0/1 status

Port    Name  Status       Vlan  Duplex  Speed  Type
Fa0/1         connected    20    a-full  a-100  10/100BaseTX

Status values:
connected      = up/up — working ✅
notconnect     = no cable / device off ❌
disabled       = admin shutdown ❌
err-disabled   = error condition ❌ (BPDU Guard, Port Security etc.)
inactive       = VLAN doesn't exist ⚠️
```

---
---

# SECTION 12 SVIs & LAYER 2/3 BOUNDARY

---

## 12.1 SVI Configuration

```
! Create and configure SVI
configure terminal

interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 description MGMT-SVI
 no shutdown
end

! Disable VLAN 1 SVI (security)
configure terminal
interface vlan 1
 no ip address
 shutdown
end
```

---

## 12.2 SVI State Rules

| Condition | SVI State | Why |
|---|---|---|
| VLAN doesn't exist in DB | down/down | No VLAN — no SVI |
| VLAN exists, no active ports | up/down | VLAN present but no L1 path |
| VLAN exists, at least one port up OR trunk carrying VLAN up | up/up | ✅ Fully operational |
| Interface manually shut | admin down/down | Explicit shutdown |

---
---

# SECTION 13 MASTER COMMAND REFERENCE TABLE

---

## Every Show Command Categorized

```
! ════════════════════════════════════
!  BASIC SWITCH INFO
! ════════════════════════════════════
show version
show running-config
show startup-config
show flash
show inventory
show environment
show platform
show system mtu

! ════════════════════════════════════
!  INTERFACES
! ════════════════════════════════════
show interfaces
show interfaces FastEthernet0/1
show interfaces FastEthernet0/1 status
show interfaces FastEthernet0/1 switchport
show interfaces FastEthernet0/1 trunk
show interfaces status
show interfaces counters
show interfaces counters errors
show ip interface brief

! ════════════════════════════════════
!  VLANs
! ════════════════════════════════════
show vlan brief
show vlan
show vlan id 20
show vlan name DATA-SALES
show vlan summary

! ════════════════════════════════════
!  TRUNKING
! ════════════════════════════════════
show interfaces trunk
show dtp
show dtp interface GigabitEthernet1/0/1

! ════════════════════════════════════
!  VTP
! ════════════════════════════════════
show vtp status
show vtp counters
show vtp password

! ════════════════════════════════════
!  MAC ADDRESS TABLE
! ════════════════════════════════════
show mac address-table
show mac address-table dynamic
show mac address-table static
show mac address-table count
show mac address-table vlan 20
show mac address-table aging-time

! ════════════════════════════════════
!  ETHERCHANNEL
! ════════════════════════════════════
show etherchannel summary
show etherchannel detail
show etherchannel port-channel
show interfaces port-channel 1
show lacp neighbor
show lacp counters
show lacp sys-id
show pagp neighbor
show pagp counters
show port-channel load-balance

! ════════════════════════════════════
!  SPANNING TREE
! ════════════════════════════════════
show spanning-tree
show spanning-tree summary
show spanning-tree summary totals
show spanning-tree active
show spanning-tree vlan 10
show spanning-tree vlan 10 detail
show spanning-tree vlan 10 bridge
show spanning-tree vlan 10 root
show spanning-tree mst
show spanning-tree mst 1
show spanning-tree mst configuration
show spanning-tree mst detail
show spanning-tree inconsistentports
show spanning-tree blockedports

! ════════════════════════════════════
!  STP SECURITY
! ════════════════════════════════════
show errdisable recovery
show errdisable detect
show interfaces status err-disabled
show spanning-tree summary

! ════════════════════════════════════
!  LAYER 2 SECURITY
! ════════════════════════════════════
show port-security
show port-security interface Fa0/1
show port-security address
show ip dhcp snooping
show ip dhcp snooping binding
show ip dhcp snooping statistics
show ip arp inspection
show ip arp inspection vlan 20
show ip arp inspection statistics
show ip arp inspection interfaces
show ip verify source

! ════════════════════════════════════
!  CDP & LLDP
! ════════════════════════════════════
show cdp neighbors
show cdp neighbors detail
show cdp interface
show cdp traffic
show lldp neighbors
show lldp neighbors detail
show lldp interface
show lldp traffic
```

---
---

# SECTION 14 QUICK TROUBLESHOOTING GUIDE

---

## Common Layer 2 Problems & Fixes

| Symptom | Likely Cause | Verification Command | Fix |
|---|---|---|---|
| PCs can't communicate | Wrong VLAN assignment | `show vlan brief` | Assign correct VLAN to access port |
| SVI is down/down | VLAN doesn't exist | `show vlan brief` | Create VLAN in database |
| SVI is up/down | No active ports in VLAN | `show interfaces trunk` | Bring up trunk or add port to VLAN |
| No VTP sync | Domain name mismatch | `show vtp status` | Match domain name & password |
| EtherChannel not forming | Mode mismatch | `show etherchannel summary` | Fix mode (active+passive, desirable+auto) |
| Native VLAN mismatch | Different native VLAN on trunk ends | `show interfaces trunk` | Match native VLAN both sides |
| Port in err-disabled | BPDU Guard or Port Security triggered | `show interfaces status` | Shut/no shut + fix root cause |
| STP loop (high CPU) | Missing STP protection | `show spanning-tree` | Enable PortFast + BPDU Guard |
| DHCP not working | DHCP Snooping blocking | `show ip dhcp snooping` | Trust the uplink port |
| ARP not resolving | DAI blocking | `show ip arp inspection stats` | Trust uplink, check binding table |
| All traffic dropped | IP Source Guard before bindings | `show ip verify source` | Ensure DHCP bindings exist first |
| Trunk not forming | DTP mismatch or missing encapsulation | `show interfaces switchport` | Set explicit trunk + encapsulation |
| VLANs missing on access | VTP transparent blocking propagation | `show vtp status` | Manually create VLANs on transparent switch |

---

## Golden Rules Never Forget

```
1.  Native VLAN must MATCH on both ends of every trunk
2.  Never rely on DTP — always configure trunks explicitly
3.  PortFast ONLY on end-device ports — NEVER on switch uplinks
4.  BPDU Guard err-disables — always have recovery plan
5.  Root Guard on downlinks — BPDU Guard on edge ports
6.  EtherChannel members must be IDENTICAL (speed/duplex/VLAN config)
7.  IP Source Guard REQUIRES DHCP Snooping bindings to exist first
8.  DAI REQUIRES DHCP Snooping trust model to be correct
9.  VTP Client CANNOT create VLANs — only Server or Transparent can
10. MST region config must be IDENTICAL on all switches in region
11. VLAN 1 = default — never use as native VLAN or management VLAN
12. passive+passive (LACP) or auto+auto (PAgP) = channel NEVER forms
13. Static EtherChannel (on) has NO error detection — use LACP/PAgP
14. VTP revision danger — always reset to 0 before adding new switch
15. Loop Guard protects against unidirectional links — not loops from config
```
