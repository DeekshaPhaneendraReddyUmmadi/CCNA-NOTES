# Routing Table & Path Selection

### Index
1. [What is Path Selection?](#1-what-is-path-selection)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: Longest Prefix Match (LPM)](#4-key-component-longest-prefix-match-lpm)
5. [Key Component: Administrative Distance (AD)](#5-key-component-administrative-distance-ad)
6. [Key Component: Metric](#6-key-component-metric)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created Path Selection Logic](#8-who-created-path-selection-logic)
9. [Types / Variations of Routing](#9-types--variations-of-routing)
10. [Flow of Phases / How it Works](#9-flow-of-phases--how-it-works)
11. [States and Timers](#10-states-and-timers)
12. [Advanced / Extra Features](#11-advanced--extra-features)
13. [Related Commands & Configuration](#12-related-commands--configuration)

---

## 1. What is Path Selection?

- **Path selection** is how a router **chooses the single best route** when it knows several ways to reach the same destination.
- It uses a strict **3-step priority order**: Longest Prefix Match → Administrative Distance → Metric.
- Its primary goal: to make sure every packet takes the **most specific, most trusted, and lowest-cost** path available.

---

## 2. Why do we need it? (The Problem it Solves)

- A router often learns about the **same destination from multiple sources** (static, OSPF, RIP) or with overlapping networks.
- Without rules, the router wouldn't know **which path to trust** — causing confusion or bad routing.
- Path selection solves the **"too many choices" problem** by applying clear tie-breakers so exactly **one best route** wins.

---

## 3. How it relates to the broader network

- Path selection is the **brain behind the routing table** — it decides what actually gets installed and used.
- It works with **static routes**, every **dynamic protocol** (RIP, OSPF, EIGRP, BGP), and **connected routes**.
- **Example:** Think of it like **choosing directions from multiple GPS apps**. First you follow the **most detailed** map (LPM), then you trust the **most reliable** app (AD), and finally you pick the **shortest** route it suggests (Metric).

---

## 4. Key Component: Longest Prefix Match (LPM)

* **What it is:** The router always picks the route with the **most specific** (longest) subnet mask that matches the destination.
* **Why it matters:** A more specific route (e.g., `/24`) points to a **smaller, more exact** network than a general one (e.g., `/16`).
* **Example:** Like a mailing address — **"Apartment 4B, 12 Main St"** (specific) beats just **"Main St"** (general). The mail goes to the exact door.

---

## 5. Key Component: Administrative Distance (AD)

* **What it is:** A **trust rating** (0–255) for each route *source*; **lower = more trusted**.
* **Why it matters:** When two *different sources* offer a route to the same network, AD breaks the tie.
* **Example:** Like trusting a **government map (low AD)** over a **stranger's hand-drawn note (high AD)** — you believe the more reliable source.

**Common AD values (lower wins):**

| Route Source | AD |
|--------------|----|
| Connected | 0 |
| Static | 1 |
| EIGRP (internal) | 90 |
| OSPF | 110 |
| RIP | 120 |
| External / Unusable | 255 |

---

## 6. Key Component: Metric

* **What it is:** A **cost value** used to pick the best path **within the same routing protocol**.
* **Why it matters:** When one protocol offers multiple paths, the **lowest metric** (cheapest) wins.
* **Example:** Like picking between two routes on the *same* GPS app — you choose the one with **fewer miles or less traffic**.

**How each protocol measures "cost":**

| Protocol | Metric Based On |
|----------|-----------------|
| RIP | Hop count |
| OSPF | Cost (based on bandwidth) |
| EIGRP | Bandwidth + delay |
| BGP | Path attributes |

---

## 7. Safety & Security Features

* **What it is:** Path selection prevents **routing loops** and **black holes** by always choosing one consistent best path, supported by **TTL** and **route validation/authentication** in protocols.
* **Why it matters:** Inconsistent choices could send packets in circles or into dead ends — path selection keeps forwarding **predictable and stable**.
* **Example:** Like a **single agreed-upon traffic plan** for a whole city — everyone follows the same rules, so no cars get stuck looping forever.

---

## 8. Who created Path Selection Logic

* **Cisco Systems** (Administrative Distance concept) and the **IETF** (standard routing logic).

    **Quick facts:**
    * **Role:** Developers of structured route-selection rules.
    * **Invention/Creation:** Administrative Distance (Cisco) to rank route sources; LPM is core to IP forwarding (IETF).
    * **Purpose:** To give routers a clear, repeatable way to choose the best path.
    * **Recognition/Standard:** LPM is defined in **IETF RFCs**; AD is a widely adopted Cisco convention.

---

## 9. Types / Variations of Path Selection

- **Longest Prefix Match (Always First)**
- **Administrative Distance (Between Sources)**
- **Metric (Within a Protocol)**

#### Longest Prefix Match
* **What it is:** Most specific subnet wins — checked **before everything else**.
* **Why use it:** Ensures packets go to the most exact destination.
* **Example:** Like delivering to a **specific apartment** over a whole street.

#### Administrative Distance
* **What it is:** Trust-based tie-breaker **between different route sources**.
* **Why use it:** Picks the most reliable source when prefixes are equal.
* **Example:** Like trusting an **official map over a rumor**.

#### Metric
* **What it is:** Cost-based tie-breaker **within the same protocol**.
* **Why use it:** Picks the cheapest path among equally-trusted options.
* **Example:** Like choosing the **shortest of two same-app routes**.

---

## 10. Flow of Phases / How it Works

### Phase 1: Find All Matching Routes
* **The Action:** The router lists every route that matches the destination IP.
* **The Logic:** It gathers all candidates from connected, static, and dynamic sources.
* **The Result:** A shortlist of possible paths is formed.

### Phase 2: Apply Longest Prefix Match
* **The Action:** The router keeps only the **most specific** (longest mask) matches.
* **The Logic:** A more specific route is always preferred — this **overrides** AD and metric.
* **The Result:** The candidate list narrows to the most exact routes.

### Phase 3: Compare Administrative Distance
* **The Action:** If candidates come from **different sources**, compare their AD.
* **The Logic:** The **lowest AD** (most trusted) source wins.
* **The Result:** Routes from less-trusted sources are dropped.

### Phase 4: Compare Metric
* **The Action:** If routes are from the **same protocol**, compare their metric.
* **The Logic:** The **lowest metric** (cheapest path) wins.
* **The Result:** The single best route is installed in the routing table.

**Example in the Field:** Imagine you need directions to a friend's exact apartment. First, you grab the **most detailed map** that shows their specific building, not just the street (LPM). You have two maps — you trust the **official city map over a stranger's sketch** (AD). The official map shows two roads, so you pick the **shorter one** (Metric). Now you have **one clear best route** — and off you go.

---

## 11. States and Timers

- **Installed (Active) Route:** The winning route placed in the routing table (RIB → FIB).
- **Backup/Inactive Route:** A valid but not-chosen route, held in case the best one fails.
- **No Direct Timers:** Path selection itself is **instant logic**, but it depends on protocol timers (e.g., OSPF/RIP update intervals) for *when* routes appear or expire.
- **Convergence:** The time for all routers to re-run path selection and agree after a topology change.

---

## 12. Advanced / Extra Features

- **Equal-Cost Multi-Path (ECMP):** If two routes tie on *all* criteria, the router **load-balances** across both.
- **Floating Static Routes:** A static route with a **deliberately high AD** so it only activates if the dynamic route fails.
- **Route Redistribution:** Sharing routes between different protocols — AD becomes critical to avoid conflicts.
- **Administrative Distance Tuning:** Manually changing AD to **prefer one source over another**.
- **RIB vs FIB:** The **RIB** (Routing Information Base) holds all routes; the **FIB** (Forwarding Information Base) holds the chosen ones for fast hardware forwarding.

---

## 13. Related Commands & Configuration

* **Command:** `show ip route`
* **What it does:** Displays the routing table — the *winners* of path selection.
* **When to use it:** **Verifying** — to see which routes are actually being used.

---

* **Command:** `show ip route [ip-address]`
* **What it does:** Shows exactly which route a specific destination IP would match.
* **When to use it:** **Troubleshooting** — to confirm LPM is choosing the route you expect.

---

* **Command:** `show ip route [network] [mask] longer-prefixes`
* **What it does:** Lists all more-specific routes under a given network.
* **When to use it:** **Troubleshooting** — to inspect LPM behavior.

---

* **Command:** `show ip protocols`
* **What it does:** Displays running protocols, their metrics, and AD values.
* **When to use it:** **Verifying** — to check how each protocol is weighted.

---

* **Command:** `distance [value]`
* **What it does:** Manually changes the Administrative Distance for a protocol or route.
* **When to use it:** **Setup/tuning** — to prefer one route source over another.

---

* **Command:** `ip route [network] [mask] [next-hop] [AD-value]`
* **What it does:** Creates a static route with a custom AD (used for **floating static** backups).
* **When to use it:** **Setup** — to build a backup path that only activates on failure.

---

* **Command:** `show ip cef`
* **What it does:** Displays the FIB — the optimized forwarding table built from chosen routes.
* **When to use it:** **Troubleshooting** — to verify what hardware actually uses to forward.

---

**Quick memory trick — the order NEVER changes:**

> **"Prefix → Police → Price"**
> 1. **Prefix** (Longest Prefix Match) — most specific always wins **first**.
> 2. **Police** (Administrative Distance) — most *trusted source* wins next.
> 3. **Price** (Metric) — cheapest *cost* wins last.

LPM is checked **before** AD, and AD **before** metric — a more specific route beats a more trusted one!

---