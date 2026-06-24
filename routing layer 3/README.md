# Routing (Layer 3)

### Index
1. [What is Routing?](#1-what-is-routing)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: The Router](#4-key-component-the-router)
5. [Key Component: The Routing Table](#5-key-component-the-routing-table)
6. [Key Component: IP Addresses & Next Hop](#6-key-component-ip-addresses--next-hop)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created Routing](#8-who-created-routing)
9. [Types / Variations of Routing](#9-types--variations-of-routing)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)
14. [Section Map (What's in This Folder)](#14-section-map)

---

## 1. What is Routing?

- **Routing** is the process of moving data **between different networks** — deciding the best path from a source to a destination across the world.
- It works at **Layer 3 (Network Layer)** using **IP addresses** (not MAC addresses).
- Its primary goal: to get a packet from **network A to network B**, even if they're on opposite sides of the planet.

---

## 2. Why do we need it? (The Problem it Solves)

- A switch can only connect devices **inside one local network** — it has no idea how to reach a different network.
- Without routing, your home network could **never reach Google, YouTube, or any website**.
- Routing solves the **"how do I reach a network that isn't mine?" problem** by finding paths across many connected networks.

---

## 3. How it relates to the broader network

- Routing sits **above switching**: switches handle local delivery (Layer 2), routers handle network-to-network delivery (Layer 3).
- It works with **IP addressing**, **ARP** (to find MAC addresses for the next hop), and **routing protocols** (to learn paths).
- **Example:** Think of routing like the **international postal system**. Your local mail carrier (switch) delivers within your town, but to send a letter to another country, it passes through **sorting centers (routers)** that each decide the next best leg of the journey.

---

## 4. Key Component: The Router

* **What it is:** A device that connects **multiple networks** and forwards packets between them based on IP.
* **Why it matters:** It's the **decision-maker** that picks where each packet goes next.
* **Example:** Like a **traffic control tower** directing planes (packets) to the right runways (networks).

---

## 5. Key Component: The Routing Table

* **What it is:** A list inside the router of **known networks** and how to reach each one.
* **Why it matters:** It's the **map** the router checks for every single packet.
* **Example:** Like a **GPS map** that knows which road leads to which city.

---

## 6. Key Component: IP Addresses & Next Hop

* **What it is:** The **destination IP** tells *where* to go; the **next hop** is the *next router* to forward to along the way.
* **Why it matters:** Routing is done **hop-by-hop** — each router only needs to know the next step, not the whole journey.
* **Example:** Like asking for directions and being told **"turn left at the next light"** — you don't need the full route, just the next move.

---

## 7. Safety & Security Features

* **What it is:** Built-in protections include the **TTL (Time To Live)** field that stops endless loops, plus **Access Control Lists (ACLs)** and **route authentication** in protocols.
* **Why it matters:** TTL prevents packets from circling forever; ACLs and authentication block unwanted or fake routing data.
* **Example:** Like a **package with an expiry stamp** (TTL) that gets discarded if it's been bouncing around too long, plus **ID checks** (authentication) at each sorting center.

---

## 8. Who created Routing

* **The early ARPANET engineers** and the **IETF**, building on the work of Internet pioneers like **Vint Cerf** and **Bob Kahn**.

    **Quick facts:**
    * **Role:** Co-creators of the TCP/IP protocol suite that makes routing possible.
    * **Invention/Creation:** TCP/IP (1970s), the foundation of how packets are addressed and routed.
    * **Purpose:** To let independent networks interconnect into one global "internetwork."
    * **Recognition/Standard:** Defined through **IETF RFCs**, the open rulebooks of the internet.

---

## 9. Types / Variations of Routing

- **Connected Routes**
- **Static Routing**
- **Dynamic Routing**
- **Default Routing**

#### Connected Routes
* **What it is:** Networks the router is **directly attached to** (automatically known).
* **Why use it:** No configuration needed — the router learns them instantly.
* **Example:** Like knowing the **rooms in your own house** without a map.

#### Static Routing
* **What it is:** Paths **manually typed in** by the administrator.
* **Why use it:** Precise control for small or simple networks.
* **Example:** Like a **hand-drawn map** you make yourself.

#### Dynamic Routing
* **What it is:** Routers **learn paths automatically** by talking to each other.
* **Why use it:** Scales to large networks and adapts to failures.
* **Example:** Like a **live GPS** that reroutes around traffic.

#### Default Routing
* **What it is:** A "catch-all" route (`0.0.0.0/0`) used when nothing else matches.
* **Why use it:** To send unknown traffic toward the internet/gateway.
* **Example:** Like a **"When unsure, go to the main exit"** sign.

---

## 10. Flow of Phases / How it Works

### Phase 1: Packet Arrival
* **The Action:** A packet arrives at the router's interface.
* **The Logic:** The router reads the **destination IP** from the packet header.
* **The Result:** The router knows *where* the packet wants to go.

### Phase 2: Routing Table Lookup
* **The Action:** The router searches its routing table for a matching network.
* **The Logic:** It uses **Longest Prefix Match** to find the most specific route.
* **The Result:** The best path (next hop + exit interface) is chosen.

### Phase 3: Forwarding Decision
* **The Action:** The router decrements the **TTL** and finds the next-hop MAC via ARP.
* **The Logic:** If a route exists, forward it; if not, use the default route or drop it.
* **The Result:** The packet is sent out the correct interface.

### Phase 4: Hop-by handles its own next step, passing the packet along the chain.
* **The Result:** The packet eventually reaches its destination network.

**Example in the Field:** Picture sending a **letter from India to Canada**. Your local post office (first router) reads the country on the envelope (destination IP), checks its sorting chart (routing table), and forwards it to the nearest international hub (next hop). Each hub along the way repeats this — none knows the *whole* route, just the **next best leg** — until the letter lands in the right Canadian city.

---

## 11. States and Timers

- **Up/Down Interface States:** A route is only usable if its exit interface is **up**.
- **TTL (Time To Live):** A counter (often starting at 64 or 128) that **drops by 1 at each hop**; at 0, the packet is discarded.
- **Route Aging (Dynamic):** Learned routes have timers — if updates stop arriving, the route is removed (varies per protocol).
- **Convergence Time:** How long all routers take to **agree on the new best paths** after a change.

---

## 12. Advanced / Extra Features

- **Equal-Cost Multi-Path (ECMP):** Load-balances traffic across multiple equal-cost routes.
- **Route Summarization:** Combines many routes into one to shrink routing tables.
- **Floating Static Routes:** Backup routes that activate only when the main path fails.
- **Policy-Based Routing (PBR):** Routes based on custom rules, not just destination IP.
- **VRF (Virtual Routing & Forwarding):** Runs multiple independent routing tables on one router.

---

## 13. Related Commands & Configuration

* **Command:** `show ip route`
* **What it does:** Displays the full routing table — all known networks and how to reach them.
* **When to use it:** **Verifying & troubleshooting** — the #1 command to check routing.

---

* **Command:** `ip route [network] [mask] [next-hop]`
* **What it does:** Manually adds a static route.
* **When to use it:** **Setup** — for small networks or specific paths.

---

* **Command:** `ip route 0.0.0.0 0.0.0.0 [next-hop]`
* **What it does:** Creates a default route (gateway of last resort).
* **When to use it:** **Setup** — to send all unknown traffic toward the internet.

---

* **Command:** `ip routing`
* **What it does:** Enables routing on a Layer 3 switch.
* **When to use it:** **Setup** — to turn on the "router brain" in a multilayer switch.

---

* **Command:** `show ip protocols`
* **What it does:** Shows which dynamic routing protocols are running and their settings.
* **When to use it:** **Verifying** — to confirm protocol configuration.

---

* **Command:** `ping [ip]` / `traceroute [ip]`
* **What it does:** Tests reachability (ping) and shows the hop-by-hop path (traceroute).
* **When to use it:** **Troubleshooting** —