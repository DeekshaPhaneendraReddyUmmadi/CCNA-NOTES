# VLANs (Virtual Local Area Networks)

### Index
1. [What is a VLAN?](#1-what-is-a-vlan)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: VLAN ID](#4-key-component-vlan-id)
5. [Key Component: Access Ports](#5-key-component-access-ports)
6. [Key Component: Trunk Ports](#6-key-component-trunk-ports)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created the VLAN](#8-who-created-the-vlan)
9. [Types / Variations of VLANs](#9-types--variations-of-vlans)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is a VLAN?

- A **VLAN** is a way to split **one physical switch** into **many separate logical networks**.
- Devices in the same VLAN can talk to each other as if they're on their own private switch — even if they're plugged into the same physical box.
- Its primary goal: to **group devices by purpose**, not by physical location.

---

## 2. Why do we need it? (The Problem it Solves)

- Without VLANs, **every device on a switch shares one big broadcast network** — noisy, insecure, and hard to manage.
- It solves the **"everyone hears everything" problem** by creating isolated groups.
- It improves **security** (departments can't snoop on each other), **performance** (less broadcast noise), and **flexibility** (move people logically without rewiring).

---

## 3. How it relates to the broader network

- VLANs operate at **Layer 2** but require **Layer 3 (a router or Layer 3 switch)** to communicate *between* different VLANs.
- They work alongside **802.1Q tagging** (which labels frames) and **Spanning Tree Protocol** (which prevents loops per VLAN).
- **Example:** Think of a VLAN like **floors in an office building**. Everyone on the same floor walks around freely, but to visit another floor you must take the **elevator (router)**. The building is one structure, but each floor stays its own private space.

---

## 4. Key Component: VLAN ID

* **What it is:** A number (from **1 to 4094**) that uniquely identifies each VLAN.
* **Why it matters:** It's the **label** switches use to keep different groups' traffic separated.
* **Example:** Like **apartment numbers** in a building — the same address, but each unit is distinct and private.

---

## 5. Key Component: Access Ports

* **What it is:** A switch port assigned to **one single VLAN**, connecting to end devices like PCs or printers.
* **Why it matters:** It places the connected device into the correct group automatically.
* **Example:** Like a **doorway into one specific apartment** — it only leads to that one home.

---

## 6. Key Component: Trunk Ports

* **What it is:** A port that carries **many VLANs at once** using 802.1Q tags, usually between switches.
* **Why it matters:** It lets multiple VLANs travel across a single cable without mixing.
* **Example:** Like a **highway with labeled lanes** — many destinations share one road, but each stays in its own marked lane.

---

## 7. Safety & Security Features

* **What it is:** Built-in protections include **VLAN isolation**, **Private VLANs**, and defenses against **VLAN hopping** (like disabling unused ports and using a dedicated native VLAN).
* **Why it matters:** It stops attackers from jumping between VLANs and keeps sensitive groups (like Finance) sealed off.
* **Example:** Like **keycard-locked floors** in a hotel — your card only opens your floor, and you can't sneak into the executive suite.

---

## 8. Who created the VLAN

* **W. David Sincoskie** at **Bellcore**, with the standard later defined by the **IEEE**.

    **Quick facts:**
    * **Role:** Pioneer of VLAN technology.
    * **Invention/Creation:** Early VLAN concepts in the late 1980s to scale Ethernet networks.
    * **Purpose:** To segment large networks logically without physical rewiring.
    * **Recognition/Standard:** Standardized as **IEEE 802.1Q**, the global VLAN tagging standard.

---

## 9. Types / Variations of VLANs

- **Data VLAN**
- **Voice VLAN**
- **Management VLAN**
- **Native VLAN**
- **Default VLAN**

#### Data VLAN
* **What it is:** Carries normal user traffic (PCs, laptops, files).
* **Why use it:** To group everyday user data separately.
* **Example:** Like the **general office workspace** where staff do daily work.

#### Voice VLAN
* **What it is:** A dedicated VLAN for **IP phone traffic**.
* **Why use it:** Gives voice calls priority so they stay clear and uninterrupted.
* **Example:** Like a **reserved express lane** just for emergency vehicles.

#### Management VLAN
* **What it is:** A VLAN used to **administer the switch itself** (e.g., remote login).
* **Why use it:** Keeps admin access separate and secure from user traffic.
* **Example:** Like a **staff-only back office** the public never sees.

#### Native VLAN
* **What it is:** The one VLAN on a trunk whose frames travel **untagged**.
* **Why use it:** For backward compatibility with devices that don't understand tags.
* **Example:** Like a **default lane** on the highway with no posted sign.

#### Default VLAN
* **What it is:** **VLAN 1** — where all switch ports belong out of the box.
* **Why use it:** It's the starting point before custom VLANs are configured.
* **Example:** Like the **lobby** everyone enters before being directed elsewhere.

---

## 10. Flow of Phases / How it Works

### Phase 1: VLAN Assignment
* **The Action:** A device sends a frame into an access port.
* **The Logic:** The switch checks which VLAN that port belongs to.
* **The Result:** The frame is **internally associated** with that VLAN.

### Phase 2: Tagging (On Trunks)
* **The Action:** If the frame must cross a trunk, the switch adds an **802.1Q tag**.
* **The Logic:** The tag carries the VLAN ID so the next switch knows the group.
* **The Result:** The frame travels labeled and stays separated.

### Phase 3: Forwarding Within VLAN
* **The Action:** The switch forwards the frame only to ports in the **same VLAN**.
* **The Logic:** It uses the VLAN ID plus the MAC table to choose ports.
* **The Result:** Traffic stays contained to its group.

### Phase 4: Inter-VLAN Routing (If Needed)
* **The Action:** If the destination is in another VLAN, the frame goes to a **router or Layer 3 switch**.
* **The Logic:** The router decides how to move data between VLANs using IP.
* **The Result:** Different VLANs communicate in a controlled way.

**Example in the Field:** Picture an **office building with floor security**. A worker (frame) badges into their floor (VLAN assignment). To move between floors, they take the **elevator (trunk)** which knows exactly which floor they belong to (tag). They roam freely on their own floor (forwarding within VLAN), but to reach another department, they must pass through the **front desk / security checkpoint (router)** first.

---

## 11. States and Timers

- **Active State:** VLAN is created and operational.
- **Suspended State:** VLAN exists but is not passing traffic.
- **STP Per-VLAN Timers:** Spanning Tree runs per VLAN with timers like **Hello (2s)**, **Forward Delay (15s)**, and **Max Age (20s)**.
- **VTP Timers:** When using VLAN Trunking Protocol, advertisements are sent every **300 seconds (5 minutes)**.

---

## 12. Advanced / Extra Features

- **VTP (VLAN Trunking Protocol):** Automatically syncs VLAN info across multiple switches.
- **Private VLANs (PVLANs):** Further isolate ports *within* a single VLAN for extra security.
- **Voice VLAN with QoS:** Prioritizes phone calls automatically.
- **Dynamic VLAN Assignment:** Assigns VLANs based on user identity (via 802.1X / RADIUS).
- **VLAN Pruning:** Stops unnecessary VLAN traffic from flooding trunks, saving bandwidth.

---

## 13. Related Commands & Configuration

* **Command:** `vlan [id]`
* **What it does:** Creates a new VLAN with the given ID and enters VLAN config mode.
* **When to use it:** **Setup** — the first step in defining a new VLAN.

---

* **Command:** `name [vlan-name]`
* **What it does:** Assigns a friendly name to a VLAN (e.g., "Finance").
* **When to use it:** **Setup** — to make VLANs easy to identify.

---

* **Command:** `switchport mode access`
* **What it does:** Sets a port to access mode (carries one VLAN to an end device).
* **When to use it:** **Setup** — for ports connecting to PCs, printers, or servers.

---

* **Command:** `switchport access vlan [id]`
* **What it does:** Assigns the access port to a specific VLAN.
* **When to use it:** **Setup** — to place a device into the correct group.

---

* **Command:** `switchport mode trunk`
* **What it does:** Turns a port into a trunk carrying multiple VLANs with tags.
* **When to use it:** **Setup** — for switch-to-switch or switch-to-router links.

---

* **Command:** `switchport trunk native vlan [id]`
* **What it does:** Sets which VLAN travels untagged across the trunk.
* **When to use it:** **Setup/security** — best practice is to set this to an unused VLAN.

---

* **Command:** `switchport trunk allowed vlan [list]`
* **What it does:** Restricts which VLANs are permitted across the trunk.
* **When to use it:** **Setup/security** — to prune unnecessary VLANs.

---

* **Command:** `show vlan brief`
* **What it does:** Displays all VLANs and which ports belong to each.
* **When to use it:** **Verifying** — to confirm VLANs and port assignments.

---

* **Command:** `show interfaces trunk`
* **What it does:** Shows trunk status and which VLANs are active on trunks.
* **When to use it:** **Verifying & troubleshooting** — to confirm tags are passing correctly.

---

* **Command:** `show interfaces switchport`
* **What it does:** Displays detailed VLAN and port mode information per interface.
* **When to use it:** **Troubleshooting** — to check access/trunk settings and native VLAN.

---
