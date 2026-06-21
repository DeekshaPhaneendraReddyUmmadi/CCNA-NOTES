# EtherChannel

### Index
1. [What is EtherChannel?](#1-what-is-etherchannel)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: Physical Member Links](#4-key-component-physical-member-links)
5. [Key Component: Port-Channel Interface](#5-key-component-port-channel-interface)
6. [Key Component: Load-Balancing Algorithm](#6-key-component-load-balancing-algorithm)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created EtherChannel](#8-who-created-etherchannel)
9. [Types / Variations of EtherChannel](#9-types--variations-of-etherchannel)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is EtherChannel?

- **EtherChannel** bundles **several physical links** between two switches into **one single logical link**.
- To the network, the bundle looks and behaves like **one big, fast connection**.
- Its primary goal: to **increase bandwidth** and provide **backup paths** without creating loops.

---

## 2. Why do we need it? (The Problem it Solves)

- A single cable between switches can become a **bottleneck** and is a **single point of failure**.
- Adding more cables normally triggers **Spanning Tree** to block the extras (to prevent loops) — wasting them.
- EtherChannel solves the **"wasted backup links" problem** by letting all the cables work **together at once** as one link, so none are blocked.

---

## 3. How it relates to the broader network

- EtherChannel works at **Layer 2 or Layer 3** and sits underneath **Spanning Tree Protocol**, which treats the whole bundle as **one link** (so it never blocks the members).
- It carries **VLAN trunks**, supports **load balancing**, and integrates with negotiation protocols (LACP/PAgP).
- **Example:** Think of EtherChannel like **adding more lanes to a single highway**. Instead of one crowded lane (and traffic police blocking the spare lanes), all lanes open up as **one wide road** — more cars flow, and if one lane closes, the rest keep traffic moving.

---

## 4. Key Component: Physical Member Links

* **What it is:** The individual physical cables/ports (usually 2 to 8) grouped into the bundle.
* **Why it matters:** They provide the **combined bandwidth** and **redundancy** of the channel.
* **Example:** Like **multiple ropes braided into one strong cable** — together they carry far more weight.

---

## 5. Key Component: Port-Channel Interface

* **What it is:** The single **logical interface** (e.g., `Port-channel1`) representing the whole bundle.
* **Why it matters:** You configure settings **once** here, and they apply to all members.
* **Example:** Like a **team captain** who speaks for the whole team — one voice for many players.

---

## 6. Key Component: Load-Balancing Algorithm

* **What it is:** The method that decides **which physical link** each traffic flow travels on (based on MAC, IP, or port info).
* **Why it matters:** It spreads traffic across links so no single cable is overloaded.
* **Example:** Like a **toll booth manager** sending cars to different open booths so no line gets too long.

---

## 7. Safety & Security Features

* **What it is:** Negotiation protocols (**LACP/PAgP**) verify both sides agree before bundling, plus **consistency checks** ensure all member ports share matching settings (speed, duplex, VLANs).
* **Why it matters:** It prevents **misconfigured or mismatched links** from forming a broken bundle that could cause loops or outages.
* **Example:** Like a **pilot's pre-flight checklist** — both crew members confirm every switch matches before takeoff.

---

## 8. Who created EtherChannel

* **Cisco Systems**, with the open standard later defined by the **IEEE**.

    **Quick facts:**
    * **Role:** Original developer of EtherChannel technology.
    * **Invention/Creation:** EtherChannel in the mid-1990s to aggregate Ethernet links.
    * **Purpose:** To boost bandwidth and redundancy between switches.
    * **Recognition/Standard:** The open equivalent is **IEEE 802.3ad / 802.1AX (Link Aggregation)** using **LACP**.

---

## 9. Types / Variations of EtherChannel

- **LACP (Link Aggregation Control Protocol)**
- **PAgP (Port Aggregation Protocol)**
- **Static (Manual "On") Mode**
- **Layer 2 EtherChannel**
- **Layer 3 EtherChannel**

#### LACP
* **What it is:** The **open-standard (IEEE)** negotiation protocol for bundling links.
* **Why use it:** Works between **different vendors**, not just Cisco.
* **Example:** Like a **universal language** both teams understand to agree on teamwork.

#### PAgP
* **What it is:** Cisco's **proprietary** negotiation protocol.
* **Why use it:** Useful in **all-Cisco** environments.
* **Example:** Like a **private company dialect** only its own staff speaks.

#### Static (Manual "On") Mode
* **What it is:** Forces the bundle without any negotiation.
* **Why use it:** When you want a bundle with **no protocol overhead** — but it's riskier.
* **Example:** Like **shaking hands without checking IDs** — fast, but you trust there's no mistake.

#### Layer 2 EtherChannel
* **What it is:** A bundle that switches traffic and carries VLAN trunks.
* **Why use it:** For **switch-to-switch** connections inside a LAN.
* **Example:** Like a **wide internal hallway** connecting two office floors.

#### Layer 3 EtherChannel
* **What it is:** A bundle assigned an **IP address** that routes traffic.
* **Why use it:** For **high-speed routed links** between core devices.
* **Example:** Like a **multi-lane bridge** connecting two separate cities.

---

## 10. Flow of Phases / How it Works

### Phase 1: Link Grouping
* **The Action:** Multiple physical ports are assigned to the same channel group.
* **The Logic:** The switch checks that all members have **matching settings** (speed, duplex, VLANs).
* **The Result:** Eligible ports are prepared to bundle.

### Phase 2: Negotiation
* **The Action:** LACP or PAgP messages are exchanged with the other switch (unless static).
* **The Logic:** Both sides confirm they **agree** to form the bundle.
* **The Result:** The links combine into one logical Port-channel.

### Phase 3: Load Balancing
* **The Action:** Traffic is sent across the member links.
* **The Logic:** The **load-balancing algorithm** assigns each flow to a specific physical link.
* **The Result:** Traffic is spread out, maximizing total throughput.

### Phase 4: Failure Handling
* **The Action:** If one member link fails, traffic shifts to the surviving links.
* **The Logic:** The bundle stays "up" as long as **at least one link** works.
* **The Result:** **No outage** — just slightly reduced total bandwidth.

**Example in the Field:** Picture a **four-lane bridge between two towns**. Engineers open all lanes as one road (grouping & negotiation). A traffic system distributes cars evenly across lanes (load balancing). If one lane closes for repair, cars simply use the other three — **traffic keeps flowing** with no full shutdown (failure handling).

---

## 11. States and Timers

- **Bundled / Up State (P):** Member port is actively part of the channel.
- **Standalone State (I):** Port is independent (negotiation failed or pending).
- **Suspended State:** Port is blocked due to mismatched settings.
- **LACP Timers:** **Fast (1 second)** or **Slow (30 seconds)** — how often LACP keepalive packets are exchanged.
- **PAgP Timers:** Hello messages sent roughly every **30 seconds**.

---

## 12. Advanced / Extra Features

- **Multi-Chassis EtherChannel (MEC / vPC / StackWise):** Bundles links across **two separate physical switches** for full device redundancy.
- **Load-Balancing Tuning:** Choose the hashing method (src-mac, dst-ip, src-dst-port) to spread traffic more evenly.
- **Min-Links:** Sets a minimum number of active links before the bundle goes "up."
- **Max-Bundle:** Limits how many links actively carry traffic, keeping others as hot standby.
- **LACP Fast Rate:** Speeds up failure detection for quicker recovery.

---

## 13. Related Commands & Configuration

* **Command:** `interface range [ports]`
* **What it does:** Selects multiple physical ports at once to configure together.
* **When to use it:** **Setup** — the first step before grouping links.

---

* **Command:** `channel-group [number] mode active`
* **What it does:** Adds the ports to a channel group using **LACP** (active negotiation).
* **When to use it:** **Setup** — the recommended way to form a standards-based bundle.

---

* **Command:** `channel-group [number] mode desirable`
* **What it does:** Forms the bundle using Cisco's **PAgP** protocol.
* **When to use it:** **Setup** — in all-Cisco environments.

---

* **Command:** `channel-group [number] mode on`
* **What it does:** Forces a **static** bundle with no negotiation.
* **When to use it:** **Setup** — only when both sides are manually configured identically.

---

* **Command:** `interface port-channel [number]`
* **What it does:** Enters config for the logical Port-channel interface.
* **When to use it:** **Setup** — to apply settings (like trunk or IP) to the whole bundle.

---

* **Command:** `port-channel load-balance [method]`
* **What it does:** Sets how traffic is distributed across member links.
* **When to use it:** **Setup/tuning** — to balance traffic more evenly.

---

* **Command:** `show etherchannel summary`
* **What it does:** Displays all channel groups, their protocol, and member port status.
* **When to use it:** **Verifying & troubleshooting** — the go-to command to check bundle health.

---

* **Command:** `show etherchannel port-channel`
* **What it does:** Shows detailed info about the Port-channel interfaces.
* **When to use it:** **Verifying** — to inspect bundle configuration and state.

---

* **Command:** `show lacp neighbor`
* **What it does:** Displays LACP partner (neighbor) details on each link.
* **When to use it:** **Troubleshooting** — to confirm the other side is negotiating correctly.

---

* **Command:** `show interfaces etherchannel`
* **What it does:** Shows per-interface EtherChannel participation and status.
* **When to use it:** **Troubleshooting** — to see which physical ports are active or suspended.

---
