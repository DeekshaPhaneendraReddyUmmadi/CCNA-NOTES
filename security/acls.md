# `ACLs`

## Index

1. [What are ACLs?](#1-what-are-acls)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component 1 — Sequential Processing (Top-Down)](#4-key-component-1--sequential-processing-top-down)
5. [Key Component 2 — Standard vs. Extended](#5-key-component-2--standard-vs-extended)
6. [Key Component 3 — Inbound vs. Outbound Direction](#6-key-component-3--inbound-vs-outbound-direction)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created it / Standards](#8-who-created-it--standards)
9. [Types / Variations](#9-types--variations)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Configuration & Troubleshooting Workflow](#13-configuration--troubleshooting-workflow)

---

## 1. What are ACLs?

- **ACLs (Access Control Lists)** are sequential lists of `permit` or `deny` statements applied to router interfaces to filter network traffic based on IP addresses, protocols, and port numbers.
- They act as a basic, stateless firewall built directly into the switch/router ASIC.
- **Analogy** 📋: An ACL is a **bouncer at a VIP club with a clipboard**. They check every person in line from the top of the list to the bottom. If your name is on the "Permit" list, you go in. If your name is on the "Deny" list, you are kicked out immediately. If your name isn't on the list at all, the bouncer defaults to kicking you out (Implicit Deny).

## 2. Why do we need it? (The Problem it Solves)

- By default, a router forwards *everything* it has a route for. You do not want guest Wi-Fi users accessing your internal accounting servers.
- Solves:
  - **Traffic Filtering (Security)** → Blocks unauthorized traffic between subnets.
  - **Traffic Classification** → ACLs aren't just for blocking; they are used to "select" traffic for NAT (which IPs get translated), QoS (which IPs get priority), and IPsec VPNs (which IPs get encrypted).
  - **Control Plane Protection** → Restricts who can SSH or Telnet into the switch itself (VTY line ACLs).

## 3. How it relates to the broader network

- In your lab, `CORE-SW1` and `CORE-SW2` perform inter-VLAN routing. To secure this, you apply ACLs to the **SVIs** (e.g., `interface vlan 20`).
- For example, you can write an ACL that allows PC1 (VLAN 20) to ping PC2 (VLAN 30), but blocks PC1 from accessing the IP Phones (VLAN 40).

## 4. Key Component 1 — Sequential Processing (Top-Down)

- ACLs are read **Top-Down**, line by line.
- **Immediate Action:** The *instant* a packet matches a line, the router takes the action (`permit` or `deny`) and **stops reading the list**.
- **The Implicit Deny:** Every ACL has an invisible, unwritten `deny any` at the very bottom. If a packet reaches the end of the list without matching a permit statement, it is dropped. (Therefore, an ACL with only `deny` statements will drop 100% of traffic).

## 5. Key Component 2 — Standard vs. Extended

- **Standard ACLs (1-99, 1300-1999):** Can ONLY filter based on the **Source IP Address**. They are blunt instruments. Because they can't specify a destination, they must be placed as close to the *destination* as possible to avoid accidentally blocking legitimate traffic along the way.
- **Extended ACLs (100-199, 2000-2699):** Can filter based on Source IP, Destination IP, Protocol (TCP/UDP/ICMP), and specific Port Numbers (e.g., Port 80, 443, 22). These are placed as close to the *source* as possible to kill bad traffic before it wastes network bandwidth.

## 6. Key Component 3 — Inbound vs. Outbound Direction

- ACLs are applied to an interface in a specific direction from the **perspective of the router's CPU**.
- **Inbound (`in`):** The router checks the ACL *before* making a routing decision. (Best practice: kills bad traffic before wasting CPU cycles routing it).
- **Outbound (`out`):** The router makes the routing decision, switches the packet to the exit interface, and *then* checks the ACL before putting it on the wire.

## 7. Safety & Security Features

- **The `log` Keyword:** Appending `log` to the end of an ACL statement generates a syslog message every time a packet hits that rule. Crucial for auditing and catching attackers.
- **VTY Access-Classes:** Applying a Standard ACL to `line vty 0 4` ensures only specific management jump-boxes can SSH into your Core switches.

## 8. Who created it / Standards

- Packet filtering is a foundational networking concept. Cisco's implementation of numbered and named ACLs has been a core part of IOS since the 1990s.
- ACLs use **Wildcard Masks** (inverse subnet masks) to define IP ranges. A `0` means "must match exactly", and a `255` means "I don't care". (e.g., `192.168.20.0 0.0.0.255` matches the entire `/24` subnet).

## 9. Types / Variations

| Type | Description |
|------|-------------|
| **Numbered ACLs** | Legacy format. Hard to edit (originally, you couldn't insert lines between existing rules). |
| **Named ACLs** | Modern standard. Allows descriptive names (e.g., `BLOCK_GUESTS`) and uses sequence numbers (10, 20, 30) so you can easily insert/delete specific lines. |
| **VACLs (VLAN ACLs)** | Applied to an entire VLAN at Layer 2. Filters traffic *within* the same subnet (no routing required). |
| **PACLs (Port ACLs)** | Applied directly to a physical Layer 2 access port (e.g., `Fa0/1`). |

## 10. Flow of Phases / How it Works

```mermaid
flowchart TD
    A[Packet arrives at SVI 20 (Inbound)] --> B{Is an ACL applied?}
    B -- No --> C[Route Packet Normally]
    B -- Yes --> D[Read Sequence 10]
    D --> E{Does Packet Match?}
    E -- Yes --> F{Action?}
    F -- Permit --> C
    F -- Deny --> G[Drop Packet]
    E -- No --> H[Read Sequence 20]
    H --> I{Does Packet Match?}
    I -- Yes --> F
    I -- No --> J[Hit Invisible Implicit Deny]
    J --> G
```

## 11. States and Timers

- **Stateless:** Standard router ACLs are *stateless*. They do not remember connections. If you permit PC1 to talk to a web server, you must explicitly ensure the return traffic from the web server is permitted back through the ACL. (This is why Stateful Firewalls like Cisco ASAs or Firepower replaced complex ACLs at the network edge).

## 12. Advanced / Extra Features

- **Time-Based ACLs:** You can tie an ACL statement to a time-range object (e.g., `permit tcp any any eq www time-range WORK_HOURS`), blocking internet access after 5:00 PM. (Requires NTP to be perfectly synced!).
- **Object Groups:** Instead of writing 50 lines for 50 different servers, you group the IPs into an `object-group` and write a single ACL line referencing the group.

---

## 13. Configuration & Troubleshooting Workflow

> ⚙️ **Note:** We will configure a **Named Extended ACL** on `CORE-SW1`. The goal is to allow Data VLAN 30 to access the internet and ping VLAN 20, but strictly block VLAN 30 from accessing Voice VLAN 40.

### Phase 1: Port Selection & Preparation
- Determine the placement. Extended ACLs go closest to the source. The source is VLAN 30. Therefore, we apply it **inbound** on `interface vlan 30`.
- Map out your wildcard masks: VLAN 30 is `192.168.30.0 0.0.0.255`. VLAN 40 is `192.168.40.0 0.0.0.255`.

### Phase 2: Base Configuration
- Create the Named Extended ACL.
- *Rule 1:* Deny access to VLAN 40.
- *Rule 2:* Permit everything else.
```
CORE-SW1> enable
CORE-SW1# configure terminal
CORE-SW1(config)# ip access-list extended SECURE_VLAN30
! Sequence 10: Block traffic to Voice VLAN
CORE-SW1(config-ext-nacl)# 10 deny ip 192.168.30.0 0.0.0.255 192.168.40.0 0.0.0.255
! Sequence 20: Allow all other traffic (Internet, VLAN 20, etc.)
CORE-SW1(config-ext-nacl)# 20 permit ip 192.168.30.0 0.0.0.255 any
CORE-SW1(config-ext-nacl)# exit
```

### Phase 3: Hardening & Security
- Add an explicit "Deny Any Any Log" at the bottom. The implicit deny is invisible and doesn't generate logs. Adding it manually lets you see exactly what is being dropped.
- Apply the ACL to the SVI.
```
CORE-SW1(config)# ip access-list extended SECURE_VLAN30
! Sequence 30: Explicit Deny with Logging
CORE-SW1(config-ext-nacl)# 30 deny ip any any log
CORE-SW1(config-ext-nacl)# exit

! Apply Inbound to the source gateway
CORE-SW1(config)# interface vlan 30
CORE-SW1(config-if)# ip access-group SECURE_VLAN30 in
CORE-SW1(config-if)# exit
```

### Phase 4: Verification Flow
Run these `show` commands **in this order**:

```
CORE-SW1# show access-lists
CORE-SW1# show ip interface vlan 30
CORE-SW1# show logging
```

- **What to look for:**
  - `show access-lists` → Displays the ACL with sequence numbers and **hit counters**. If you ping VLAN 40 from PC2, you should see `(4 matches)` next to the deny statement.
  - `show ip interface vlan 30` → Look for the line: **Inbound access list is SECURE_VLAN30**. If it says "not set", you forgot to apply it.
  - `show logging` → If traffic hits sequence 30, you will see `%SEC-6-IPACCESSLOGP` messages detailing the exact Source IP, Dest IP, and Port that was dropped.

### Phase 5: Advanced Debugging
- If legitimate traffic is being blocked, or bad traffic is getting through:
```
CORE-SW1# show access-lists SECURE_VLAN30
CORE-SW1# clear access-list counters
```
- **Troubleshooting logic:**
  - **Everything is blocked** → You forgot the `permit ip any any` at the end. The implicit deny is dropping 100% of the traffic.
  - **ACL is ignored** → You applied it in the wrong direction. If you applied it `out` on VLAN 30, it filters traffic *leaving* the router and going *to* the PCs, not traffic coming *from* the PCs.
  - **Wrong Wildcard Mask** → You typed `255.255.255.0` instead of `0.0.0.255`. A wildcard mask is the exact inverse of a subnet mask. The router will interpret this as a completely different IP range.
  - **Editing a mistake** → Do not delete the whole ACL! Just use sequence numbers. If you need to insert a rule before line 10, go into the ACL and type `5 deny tcp host 192.168.30.50 any eq 22`.
