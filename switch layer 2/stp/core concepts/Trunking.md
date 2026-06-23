# Trunking

### Index
1. [What is Trunking?](#1-what-is-trunking)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: 802.1Q Tag](#4-key-component-8021q-tag)
5. [Key Component: Native VLAN](#5-key-component-native-vlan)
6. [Key Component: Allowed VLAN List](#6-key-component-allowed-vlan-list)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created Trunking](#8-who-created-trunking)
9. [Types / Variations of Trunking](#9-types--variations-of-trunking)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is Trunking?

- **Trunking** is a way to carry **many VLANs over a single physical link** between two switches (or a switch and a router).
- It adds a small **tag** to each frame so the receiving switch knows which VLAN it belongs to.
- Its primary goal: to let multiple separate networks **share one cable** without mixing their traffic.

---

## 2. Why do we need it? (The Problem it Solves)

- Without trunking, you'd need **one cable per VLAN** between switches — 10 VLANs would mean 10 cables!
- This wastes ports, cables, and money, and is impossible to scale.
- Trunking solves the **"too many cables" problem** by carrying all VLANs over **one shared link**, keeping each VLAN's traffic clearly labeled and separate.

---

## 3. How it relates to the broader network

- Trunking is a **Layer 2** feature that works hand-in-hand with **VLANs**, **EtherChannel** (trunks can be bundled), and **Spanning Tree** (which runs per VLAN across the trunk).
- It's the **backbone link** connecting switches, and the path for **Inter-VLAN routing** to a router or Layer 3 switch.
- **Example:** Think of a trunk like a **multi-floor service elevator** in an office building. Instead of building a separate elevator for each floor (VLAN), one elevator carries everyone — but each person wears a **floor badge (tag)** so they get off at the right level.

---

## 4. Key Component: 802.1Q Tag

* **What it is:** A small **4-byte label** inserted into each frame that records its **VLAN ID**.
* **Why it matters:** It's how the receiving switch knows **which VLAN** the frame belongs to.
* **Example:** Like a **colored wristband at an event** — staff instantly know which group you're with.

---

## 5. Key Component: Native VLAN

* **What it is:** The one VLAN on a trunk whose frames travel **untagged** (no label).
* **Why it matters:** It provides backward compatibility and must **match on both ends** to avoid traffic leaks.
* **Example:** Like the **default lane with no posted sign** — everyone assumes it's the standard one, so both sides must agree on what it is.

---

## 6. Key Component: Allowed VLAN List

* **What it is:** A setting that controls **exactly which VLANs** are permitted to cross the trunk.
* **Why it matters:** It improves **security and efficiency** by blocking unneeded VLAN traffic.
* **Example:** Like a **guest list at the elevator** — only badged groups on the list are allowed to ride.

---

## 7. Safety & Security Features

* **What it is:** Best practices include **pruning unused VLANs**, setting the native VLAN to an **unused VLAN**, and **hard-coding trunk mode** (disabling auto-negotiation) to prevent **VLAN hopping** attacks.
* **Why it matters:** Misconfigured trunks are a top cause of **security breaches** where attackers jump between VLANs.
* **Example:** Like a **secure elevator with a fixed, locked control panel** — no one can trick it into stopping on a restricted floor.

---

## 8. Who created Trunking

* **IEEE** (standard tagging) and **Cisco Systems** (early proprietary method, ISL).

    **Quick facts:**
    * **Role:** Developers of VLAN trunking methods.
    * **Invention/Creation:** Cisco's **ISL** (Inter-Switch Link) came first; the IEEE later created the open **802.1Q** standard.
    * **Purpose:** To carry multiple VLANs across a single link with proper tagging.
    * **Recognition/Standard:** **IEEE 802.1Q** is the universal, vendor-neutral trunking standard today.

---

## 9. Types / Variations of Trunking

- **802.1Q (Dot1Q) Trunking**
- **ISL (Inter-Switch Link)**
- **DTP (Dynamic Trunking Protocol) Modes**

#### 802.1Q (Dot1Q) Trunking
* **What it is:** The open IEEE standard that **inserts a tag** into the frame.
* **Why use it:** Works across **all vendors** — the modern default.
* **Example:** Like a **universal luggage tag** any airline can read.

#### ISL (Inter-Switch Link)
* **What it is:** Cisco's older **proprietary** method that **wraps** the entire frame.
* **Why use it:** Only in **legacy all-Cisco** gear (now largely obsolete).
* **Example:** Like an **old company-only shipping box** other couriers can't handle.

#### DTP (Dynamic Trunking Protocol) Modes
* **What it is:** Cisco's protocol that lets ports **auto-negotiate** trunk status.
* **Why use it:** For convenience — but best practice is to **disable it** and set trunks manually.
* **Example:** Like a **door that auto-decides whether to open** — handy, but a security risk if left on.

---

## 10. Flow of Phases / How it Works

### Phase 1: Trunk Establishment
* **The Action:** Two ports are configured (or negotiate) to become trunk ports.
* **The Logic:** Both ends agree to carry multiple VLANs and use the same encapsulation (802.1Q).
* **The Result:** A trunk link is formed, ready to carry tagged traffic.

### Phase 2: Tagging on Send
* **The Action:** A frame leaving the trunk gets an **802.1Q tag** added (unless it's the native VLAN).
* **The Logic:** The switch stamps the frame with its **VLAN ID**.
* **The Result:** The frame travels labeled, keeping VLANs separate.

### Phase 3: Carrying Across the Link
* **The Action:** Many VLANs' frames flow over the **single physical cable**.
* **The Logic:** Each frame's tag keeps it identified, so traffic never mixes.
* **The Result:** One cable efficiently carries dozens of VLANs.

### Phase 4: Untagging on Receive
* **The Action:** The receiving switch reads the tag, then **removes it** before delivering to the right VLAN.
* **The Logic:** The end device shouldn't see the tag — it just gets normal traffic.
* **The Result:** The frame reaches the correct VLAN, clean and untagged.

**Example in the Field:** Picture a **shared shuttle bus between two office buildings**. As each worker boards (tagging), they get a **floor sticker** showing their department. The bus carries everyone together over one road (carrying across). On arrival, a greeter **reads and removes the sticker** (untagging) and sends each worker to their correct floor — no one ends up in the wrong department.

---

## 11. States and Timers

- **Trunk Mode States:** `trunk`, `access`, `dynamic auto`, `dynamic desirable`.
- **Negotiated vs Static:** A trunk can form by manual config (`mode trunk`) or DTP negotiation.
- **DTP Timer:** DTP advertisements are sent roughly every **30 seconds**.
- **STP Per-VLAN:** Spanning Tree runs **per VLAN** across the trunk, each with its own Hello (2s), Forward Delay (15s), and Max Age (20s) timers.

---

## 12. Advanced / Extra Features

- **VLAN Pruning:** Automatically stops VLANs with no active members from flooding the trunk, saving bandwidth.
- **Native VLAN Tagging:** Forces even the native VLAN to be tagged for tighter security.
- **Trunk over EtherChannel:** Bundle multiple trunk links into one for speed **and** VLAN transport.
- **VTP (VLAN Trunking Protocol):** Syncs VLAN databases across switches over trunks.
- **QinQ (802.1ad):** "Double-tagging" so service providers can carry customers' VLANs inside their own.

---

## 13. Related Commands & Configuration

* **Command:** `switchport mode trunk`
* **What it does:** Statically forces the port to become a trunk.
* **When to use it:** **Setup** — the recommended, secure way for switch-to-switch links.

---

* **Command:** `switchport trunk encapsulation dot1q`
* **What it does:** Sets the trunk to use the 802.1Q tagging standard (on switches that support ISL too).
* **When to use it:** **Setup** — to choose the modern, vendor-neutral method.

---

* **Command:** `switchport trunk native vlan [id]`
* **What it does:** Sets which VLAN travels untagged across the trunk.
* **When to use it:** **Setup/security** — best practice is to set this to an **unused** VLAN.

---

* **Command:** `switchport trunk allowed vlan [list]`
* **What it does:** Restricts which VLANs are permitted to cross the trunk.
* **When to use it:** **Setup/security** — to prune unneeded VLANs (e.g., `...allowed vlan 10,20,30`).

---

* **Command:** `switchport trunk allowed vlan add [id]`
* **What it does:** Adds a VLAN to the existing allowed list **without erasing** the others.
* **When to use it:** **Setup** — to safely expand a trunk's VLANs.

---

* **Command:** `switchport nonegotiate`
* **What it does:** Disables DTP so the port won't auto-negotiate trunking.
* **When to use it:** **Security** — to prevent VLAN-hopping attacks on static trunks.

---

* **Command:** `switchport mode dynamic desirable`
* **What it does:** Actively tries to negotiate a trunk with the other side via DTP.
* **When to use it:** **Setup** — convenient, but less secure than static config.

---

* **Command:** `show interfaces trunk`
* **What it does:** Displays all trunk ports, their native VLAN, and which VLANs are allowed/active.
* **When to use it:** **Verifying & troubleshooting** — the go-to trunk health check.

---

* **Command:** `show interfaces [port] switchport`
* **What it does:** Shows detailed mode, trunk status, native VLAN, and encapsulation for a port.
* **When to use it:** **Troubleshooting** — to confirm a port's exact trunk settings.

---

* **Command:** `show dtp interface [port]`
* **What it does:** Displays DTP negotiation status on a port.
* **When to use it:** **Troubleshooting** — to see why a trunk did or didn't form.

---

**Quick tip — Trunk vs Access:**
- **Access port** → carries **one VLAN** → faces an **end device** (PC, printer).
- **Trunk port** → carries **many VLANs (tagged)** → faces **another switch or router**.

---

