# Static Routing

### Index
1. [What is Static Routing?](#1-what-is-static-routing)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: Destination Network & Mask](#4-key-component-destination-network--mask)
5. [Key Component: Next Hop / Exit Interface](#5-key-component-next-hop--exit-interface)
6. [Key Component: Administrative Distance](#6-key-component-administrative-distance)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created Static Routing](#8-who-created-static-routing)
9. [Types / Variations of Static Routing](#9-types--variations-of-static-routing)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is Static Routing?

- **Static routing** is when a network administrator **manually types in** the paths a router should use to reach other networks.
- The routes **never change on their own** — they stay exactly as configured until someone edits them.
- Its primary goal: to give **precise, predictable control** over how traffic flows.

---

## 2. Why do we need it? (The Problem it Solves)

- A router only knows about networks **directly connected** to it — everything else is unknown.
- For **small or simple networks**, running a full dynamic protocol is overkill and wastes resources.
- Static routing solves the **"how do I reach a far network without complex protocols?" problem** by letting you hard-code the exact path.

---

## 3. How it relates to the broader network

- Static routing fills the **routing table** manually, sitting alongside connected and dynamic routes.
- It works with **path selection** (it has a very low, trusted AD of **1**) and is often used for **default routes** toward the internet.
- **Example:** Think of static routing like writing **permanent directions on a sticky note** — "To reach the warehouse, always take Highway 7." It's reliable and never changes, but if Highway 7 closes, your note is now wrong until you rewrite it.

---

## 4. Key Component: Destination Network & Mask

* **What it is:** The **target network** you want to reach, plus its **subnet mask** (how big it is).
* **Why it matters:** It tells the router **which destination** this route applies to.
* **Example:** Like writing the **city and zip code** on a package so it knows where it's headed.

---

## 5. Key Component: Next Hop / Exit Interface

* **What it is:** Either the **IP of the next router** (next hop) or the **local port** (exit interface) to send traffic out of.
* **Why it matters:** It tells the router **where to forward** the packet next.
* **Example:** Like a sign saying **"Hand this package to the driver at Gate 3"** — the next step in the journey.

---

## 6. Key Component: Administrative Distance

* **What it is:** Static routes have a default **AD of 1** (very trusted — second only to connected routes at 0).
* **Why it matters:** It means a static route is usually **preferred over dynamic routes** to the same destination.
* **Example:** Like trusting a **hand-signed official order** over an automated suggestion.

---

## 7. Safety & Security Features

* **What it is:** Static routes are **immune to routing-protocol attacks** (no advertisements to spoof) and can be paired with **floating static routes** for safe backup paths.
* **Why it matters:** Nothing external can trick a static route into changing — it's as secure as the device itself.
* **Example:** Like a **locked, hand-written instruction** that no outsider can alter or fake.

---

## 8. Who created Static Routing

* **Early ARPANET / Internet engineers** and the **IETF**, as part of foundational IP routing.

    **Quick facts:**
    * **Role:** Builders of the original IP forwarding model.
    * **Invention/Creation:** Manual route entries — the earliest, simplest way to direct IP traffic.
    * **Purpose:** To let administrators define exact paths between networks.
    * **Recognition/Standard:** Part of the core **IP routing standards** defined in IETF RFCs.

---

## 9. Types / Variations of Static Routing

- **Standard Static Route**
- **Default Route**
- **Floating Static Route**
- **Host Route**
- **Fully Specified Static Route**

#### Standard Static Route
* **What it is:** A route to a **specific network** with a defined next hop.
* **Why use it:** For precise control over reaching a known destination.
* **Example:** Like a direct sign: **"To Building B, take Road 5."**

#### Default Route
* **What it is:** A catch-all route (`0.0.0.0/0`) used when no other route matches.
* **Why use it:** To send all unknown traffic toward the internet/gateway.
* **Example:** Like a **"When in doubt, use the main exit"** sign.

#### Floating Static Route
* **What it is:** A backup static route with a **higher AD** so it only activates if the primary path fails.
* **Why use it:** For redundancy without dynamic routing.
* **Example:** Like a **spare tire** — only used when the main one goes flat.

#### Host Route
* **What it is:** A static route to a **single specific device** (`/32` mask).
* **Why use it:** To control the path to one exact host.
* **Example:** Like directions to **one specific person's desk**, not the whole office.

#### Fully Specified Static Route
* **What it is:** A route that specifies **both** the exit interface **and** the next-hop IP.
* **Why use it:** Removes ambiguity on multi-access links (like Ethernet).
* **Example:** Like saying **"Use Door 3 AND hand it to driver Mike"** — zero confusion.

---

## 10. Flow of Phases / How it Works

### Phase 1: Manual Configuration
* **The Action:** The admin types an `ip route` command defining destination + next hop.
* **The Logic:** The router validates the entry (is the next hop reachable?).
* **The Result:** The route is added to the routing table.

### Phase 2: Route Installation
* **The Action:** The router installs the static route (AD 1) into its table.
* **The Logic:** Because AD is low, it usually wins over dynamic routes.
* **The Result:** The path becomes active and usable.

### Phase 3: Packet Forwarding
* **The Action:** When a matching packet arrives, the router forwards it via the static route.
* **The Logic:** Longest Prefix Match confirms this route applies.
* **The Result:** The packet heads to the configured next hop.

### Phase 4: Failure Handling
* **The Action:** If the exit interface goes **down**, the static route is **removed** from the table.
* **The Logic:** A route is only valid if its path is up; a floating static can then take over.
* **The Result:** Traffic either fails over (if a backup exists) or is dropped.

**Example in the Field:** Imagine a small office with **one road to the internet**. The admin writes a permanent note: **"All outside mail → give to the courier at the front gate"** (default static route). Every unknown package follows that note automatically. If the front gate closes (interface down), the note becomes invalid — and if there's a **back gate note (floating static)**, mail reroutes there instead.

---

## 11. States and Timers

- **Active State:** The route is installed and the exit interface is **up**.
- **Inactive/Removed State:** If the interface goes down, the route **disappears** from the table.
- **No Update Timers:** Unlike dynamic routing, static routes have **no periodic updates** — they don't expire or refresh.
- **Instant Behavior:** Changes take effect **immediately** when typed (or removed when the link drops).

---

## 12. Advanced / Extra Features

- **Floating Static for Backup:** Set a higher AD (e.g., `ip route ... 200`) so it only activates if the primary fails.
- **Recursive Routes:** A next hop that itself must be looked up in the table (router resolves it in steps).
- **Static Route with Tracking (IP SLA):** Removes the route automatically if a target stops responding — even if the interface stays up.
- **Default Route Propagation:** A static default route can be **shared into** dynamic protocols.
- **Discard/Null Routes:** Point unwanted traffic to `Null0` to safely drop it (useful for security/blackholing).

---

## 13. Related Commands & Configuration

* **Command:** `ip route [network] [mask] [next-hop]`
* **What it does:** Creates a standard static route to a network via a next-hop IP.
* **When to use it:** **Setup** — the core command for static routing.

---

* **Command:** `ip route [network] [mask] [exit-interface]`
* **What it does:** Creates a static route using a local exit interface instead of a next hop.
* **When to use it:** **Setup** — common on point-to-point links.

---

* **Command:** `ip route [network] [mask] [exit-interface] [next-hop]`
* **What it does:** A **fully specified** route — both interface and next hop.
* **When to use it:** **Setup** — to remove ambiguity on Ethernet/multi-access links.

---

* **Command:** `ip route 0.0.0.0 0.0.0.0 [next-hop]`
* **What it does:** Creates a **default route** (gateway of last resort).
* **When to use it:** **Setup** — to send all unknown traffic toward the internet.

---

* **Command:** `ip route [network] [mask] [next-hop] [AD-value]`
* **What it does:** Creates a **floating static** route with a custom (higher) AD.
* **When to use it:** **Setup** — for a backup path that activates only on failure.

---

* **Command:** `ip route [host-ip] 255.255.255.255 [next-hop]`
* **What it does:** Creates a **host route** to one specific device (`/32`).
* **When to use it:** **Setup** — for precise control to a single host.

---

* **Command:** `ip route [network] [mask] Null0`
* **What it does:** Sends matching traffic to a "black hole" (discards it).
* **When to use it:** **Security** — to safely drop unwanted traffic.

---

* **Command:** `show ip route static`
* **What it does:** Displays only the static routes in the routing table.
* **When to use it:** **Verifying** — to confirm your static routes are installed.

---

* **Command:** `no ip route [network] [mask] [next-hop]`
* **What it does:** Removes a previously configured static route.
* **When to use it:** **Setup/cleanup** — to delete an unwanted route.

---

**Quick tip — Static vs Dynamic at a glance:**

| | **Static** | **Dynamic** |
|---|-----------|-------------|
| **Set by** | Admin (manual) | Router (automatic) |
| **Adapts to failures?** | ❌ No (unless floating) | ✅ Yes |
| **Overhead** | None | CPU + bandwidth |
| **Best for** | Small/stub networks | Large/changing networks |
| **AD** | 1 | 90–120+ |

---