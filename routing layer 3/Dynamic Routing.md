# Dynamic Routing

### Index
1. [What is Dynamic Routing?](#1-what-is-dynamic-routing)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: Routing Protocol](#4-key-component-routing-protocol)
5. [Key Component: Neighbor Relationships](#5-key-component-neighbor-relationships)
6. [Key Component: Metric & Best-Path Calculation](#6-key-component-metric--best-path-calculation)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created Dynamic Routing](#8-who-created-dynamic-routing)
9. [Types / Variations of Dynamic Routing](#9-types--variations-of-dynamic-routing)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is Dynamic Routing?

- **Dynamic routing** is when routers **automatically learn paths** by talking to each other and sharing what they know.
- Instead of an admin typing every route, the routers **build and update the routing table themselves**.
- Its primary goal: to let networks **adapt automatically** when links fail, recover, or change.

---

## 2. Why do we need it? (The Problem it Solves)

- In large networks, **manually typing every route** (static) is impossible and error-prone.
- If a link fails, static routes **can't adapt** — traffic just breaks until a human fixes it.
- Dynamic routing solves the **"too big to manage by hand + must self-heal" problem** by letting routers discover and repair paths on their own.

---

## 3. How it relates to the broader network

- Dynamic routing fills the **routing table automatically**, working alongside static and connected routes.
- It relies on **path selection** (AD picks between protocols; metric picks within one) and feeds the final best paths into forwarding.
- **Example:** Think of dynamic routing like a **live GPS network** where every navigator constantly shares traffic updates. If a road closes, the whole system **reroutes automatically** — no one has to redraw the map by hand.

---

## 4. Key Component: Routing Protocol

* **What it is:** The **language and rules** routers use to share route information (e.g., OSPF, EIGRP, RIP, BGP).
* **Why it matters:** It defines **how** routers learn, share, and choose paths.
* **Example:** Like a **common language** spoken at an international meeting so everyone understands each other.

---

## 5. Key Component: Neighbor Relationships

* **What it is:** Routers first **discover and form a relationship** ("adjacency") with directly connected routers before sharing routes.
* **Why it matters:** They only trust and exchange info with **confirmed neighbors**, keeping routing reliable.
* **Example:** Like **shaking hands and introducing yourself** before sharing important information with a colleague.

---

## 6. Key Component: Metric & Best-Path Calculation

* **What it is:** Each protocol uses a **metric** (cost formula) to calculate the **best path** to each network.
* **Why it matters:** It ensures traffic takes the **most efficient** route the protocol can find.
* **Example:** Like a GPS scoring routes by **distance, traffic, and road quality** before picking the best one.

---

## 7. Safety & Security Features

* **What it is:** Most protocols support **authentication** (passwords/keys on updates), plus loop-prevention tools like **split horizon**, **route poisoning**, and **TTL/max-hop limits**.
* **Why it matters:** Authentication blocks **fake routers** from injecting bad routes; loop-prevention stops traffic from circling forever.
* **Example:** Like requiring a **secret password and ID badge** before anyone can update the shared map — and a rule that stops messages from echoing endlessly.

---

## 8. Who created Dynamic Routing

* **Internet pioneers and the IETF**, with vendors like **Cisco** developing specific protocols.

    **Quick facts:**
    * **Role:** Designers of automatic route-sharing protocols.
    * **Invention/Creation:** RIP (one of the earliest, 1980s), then OSPF, EIGRP, and BGP.
    * **Purpose:** To let networks scale and self-heal without manual route entry.
    * **Recognition/Standard:** Most are **IETF standards** (RIP, OSPF, BGP); EIGRP began as Cisco-proprietary (later opened).

---

## 9. Types / Variations of Dynamic Routing

- **Distance-Vector**
- **Link-State**
- **Hybrid / Advanced Distance-Vector**
- **Path-Vector**

#### Distance-Vector
* **What it is:** Routers share their **entire routing table** with neighbors ("routing by rumor"), judging routes by direction + distance (e.g., hop count).
* **Why use it:** Simple and low-resource for small networks.
* **Example:** Like asking **each person at a crossroads** "how far is the city this way?" and trusting their answer. *(Protocol: RIP)*

#### Link-State
* **What it is:** Every router builds a **complete map** of the whole network and calculates the best path itself.
* **Why use it:** Fast, accurate, and scales well in large networks.
* **Example:** Like every driver having the **full city map** and plotting their own best route. *(Protocol: OSPF)*

#### Hybrid / Advanced Distance-Vector
* **What it is:** Combines the **simplicity of distance-vector** with **link-state-like speed** and smarter metrics.
* **Why use it:** Fast convergence with efficient resource use.
* **Example:** Like a GPS that uses **both local tips and a full map** for the best of both. *(Protocol: EIGRP)*

#### Path-Vector
* **What it is:** Shares the **full path (list of networks/AS)** a route passes through, used between organizations.
* **Why use it:** To route across the **entire internet** with policy control.
* **Example:** Like a **shipping manifest** listing every country a package will pass through. *(Protocol: BGP)*

---

## 10. Flow of Phases / How it Works

### Phase 1: Neighbor Discovery
* **The Action:** A router sends "hello" messages to find directly connected routers.
* **The Logic:** It identifies who it can form a relationship (adjacency) with.
* **The Result:** Trusted neighbors are established.

### Phase 2: Route Exchange
* **The Action:** Neighbors **share route information** with each other.
* **The Logic:** Each router collects what others know about distant networks.
* **The Result:** Routers build a picture of all reachable networks.

### Phase 3: Best-Path Calculation
* **The Action:** Each router runs its protocol's **algorithm** to compute the best route per destination.
* **The Logic:** The lowest **metric** wins for each network.
* **The Result:** The best paths are selected and installed in the routing table.

### Phase 4: Convergence & Maintenance
* **The Action:** Routers keep exchanging updates; if a link changes, they **recalculate**.
* **The Logic:** Continuous monitoring detects failures and recovery.
* **The Result:** The network **converges** on a new, consistent set of best paths automatically.

**Example in the Field:** Picture a **fleet of delivery drivers with connected GPS units**. Each driver greets nearby drivers (neighbor discovery) and shares road info (route exchange). Every GPS calculates the fastest route (best-path). When a highway suddenly closes, the news spreads, all units **recalculate, and reroute together** within seconds (convergence) — no dispatcher needed.

---

## 11. States and Timers

- **Neighbor States:** Routers move through states (e.g., OSPF: Down → Init → 2-Way → Full) before fully exchanging routes.
- **Hello/Keepalive Timers:** Regular messages (e.g., OSPF Hello every **10s**) confirm neighbors are alive.
- **Hold/Dead Timer:** If no hello arrives in time (e.g., **40s** in OSPF), the neighbor is declared down.
- **Update Timers (Distance-Vector):** RIP sends full updates every **30 seconds**.
- **Convergence Time:** How long all routers take to agree after a change — link-state/hybrid converge **fast**, classic distance-vector **slower**.

---

## 12. Advanced / Extra Features

- **Route Summarization:** Combines many routes into one advertisement to shrink tables.
- **Authentication:** Secures updates with passwords/keys (MD5, SHA).
- **Equal-Cost Multi-Path (ECMP):** Load-balances across multiple equal-cost paths.
- **Route Redistribution:** Shares routes between different protocols (e.g., OSPF ↔ EIGRP).
- **Passive Interfaces:** Stops a protocol from sending updates out specific ports (security/efficiency).
- **Areas / Hierarchy (OSPF):** Divides large networks into zones for scalability.

---

## 13. Related Commands & Configuration

* **Command:** `router [protocol]`
* **What it does:** Enters configuration mode for a routing protocol (e.g., `router ospf 1`).
* **When to use it:** **Setup** — the first step to enable any dynamic protocol.

---

* **Command:** `network [network] [wildcard] area [id]`
* **What it does:** Tells the protocol which interfaces/networks to advertise (OSPF example).
* **When to use it:** **Setup** — to include networks in the routing process.

---

* **Command:** `show ip route`
* **What it does:** Displays the routing table, with codes showing how each route was learned (O=OSPF, D=EIGRP, R=RIP, B=BGP).
* **When to use it:** **Verifying** — to see which dynamic routes are active.

---

* **Command:** `show ip protocols`
* **What it does:** Shows running protocols, their timers, metrics, and networks.
* **When to use it:** **Verifying** — to review protocol configuration.

---

* **Command:** `show ip [protocol] neighbor`
* **What it does:** Displays neighbor relationships (e.g., `show ip ospf neighbor`).
* **When to use it:** **Troubleshooting** — to confirm adjacencies formed correctly.

---

* **Command:** `passive-interface [interface]`
* **What it does:** Stops sending routing updates out a specific interface.
* **When to use it:** **Setup/security** — on interfaces facing end users, not routers.

---

* **Command:** `[protocol] authentication ...`
* **What it does:** Enables authentication on routing updates.
* **When to use it:** **Security** — to block rogue routers from injecting routes.

---

* **Command:** `debug ip [protocol]`
* **What it does:** Shows real-time protocol activity (updates, hellos, errors).
* **When to use it:** **Troubleshooting** — use carefully; it can flood the console.

---

**Quick comparison — the four dynamic protocols:**

| Protocol | Type | Metric | AD | Best For |
|----------|------|--------|----|---------:|
| **RIP** | Distance-Vector | Hop count (max 15) | 120 | Tiny/simple networks |
| **OSPF** | Link-State | Cost (bandwidth) | 110 | Large multi-vendor |
| **EIGRP** | Hybrid (Adv. D-V) | Bandwidth + delay | 90 | Cisco enterprise |
| **BGP** | Path-Vector | Path attributes | 20/200 | Internet / between orgs |

> 📂 **Deep dives:** [RIP](./dynamic%20protocols/RIP.md) · [OSPF](./dynamic%20protocols/OSPF.md) · [EIGRP](./dynamic%20protocols/EIGRP.md) · [BGP](./dynamic%20protocols/BGP.md)

---