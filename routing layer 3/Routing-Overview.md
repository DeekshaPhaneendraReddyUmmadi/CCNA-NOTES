# Routing (Layer 3) — Master Flowchart

### Index
1. [The Complete Routing Hierarchy](#1-the-complete-routing-hierarchy)
2. [Branch 1: Routing Fundamentals](#2-branch-1-routing-fundamentals)
3. [Branch 2: How a Router Decides (The Routing Table)](#3-branch-2-how-a-router-decides-the-routing-table)
4. [Branch 3: Static Routing](#4-branch-3-static-routing)
5. [Branch 4: Dynamic Routing (with Protocols)](#5-branch-4-dynamic-routing-with-protocols)
6. [Decision Flow: How a Router Handles a Packet](#6-decision-flow-how-a-router-handles-a-packet)
7. [Suggested Folder Structure](#7-suggested-folder-structure)

---

## 1. The Complete Routing Hierarchy

The full **Layer 3 routing** picture — everything fits under one roof:

```
                              LAYER 3 ROUTING
                        (Connecting different networks)
                                    │
       ┌──────────────┬─────────────┼─────────────┬──────────────┐
       │              │             │             │              │
  FUNDAMENTALS   THE ROUTING    STATIC        DYNAMIC      (L3 SWITCH
  (the basics)   TABLE LOGIC    ROUTING       ROUTING       LINK-UP)
       │         (how it picks) (manual)      (automatic)        │
       │              │             │             │         SVI / ip routing
       │              │             │             │
   ┌───┴───┐    ┌─────┴─────┐       │       ┌─────┴───────────┐
   │       │    │     │     │       │       │            │    │
 What    Why  LPM  Admin  Metric  Default  IGP          EGP  (more)
 is it?  need     Distance        Route     │            │
                                            │            │
                                    ┌───────┼──────┐     │
                                    │       │      │     │
                                   RIP    OSPF   EIGRP  BGP
```

- **Like a global postal system:** a **switch** delivers mail **within one city** (local LAN), while a **router** decides how mail travels **between cities and countries** (different networks).

> 📂 **Start here:** [Introduction](./README.md)

---

## 2. Branch 1: Routing Fundamentals

```
ROUTING FUNDAMENTALS (The Foundation)
   │
   ├── What is Routing? ──> Moving packets BETWEEN networks (Layer 3)
   │
   ├── Routing vs Switching:
   │      ├── Switch ──> uses MAC addresses (local, Layer 2)
   │      └── Router ──> uses IP addresses (global, Layer 3)
   │
   ├── The Packet ──> Carries Source IP + Destination IP
   │
   └── Key Tools:
          ├── Routing Table   (the router's map)
          ├── Next Hop        (the next door to send to)
          └── Exit Interface  (which cable to use)
```

- **Like a GPS navigator:** it doesn't care about street names inside one town (MAC) — it plots the route **across the whole country** (IP).

> 📂 **Full guide:** [Introduction](./README.md)

---

## 3. Branch 2: How a Router Decides (The Routing Table)

When a router knows **multiple paths** to a destination, it follows a strict 3-step tie-breaker:

```
THE ROUTING TABLE LOGIC (Picking the Best Path)
   │
   ├── STEP 1: Longest Prefix Match (LPM)  ── MOST SPECIFIC wins
   │      (e.g., /24 beats /16 for the same destination)
   │
   ├── STEP 2: Administrative Distance (AD) ── MOST TRUSTED source wins
   │      (only used to pick between DIFFERENT route sources)
   │
   └── STEP 3: Metric ── LOWEST COST wins
          (only used WITHIN the same protocol)
```

**Administrative Distance cheat-sheet (lower = more trusted):**

| Route Source | AD |
|--------------|----|
| Connected | 0 |
| Static | 1 |
| EIGRP (internal) | 90 |
| OSPF | 110 |
| RIP | 120 |
| External / Unknown | 255 (unusable) |

- **Like choosing directions:** first pick the **most specific** instructions (LPM), then trust the **most reliable source** (AD), then take the **shortest** of equal options (metric).

> 📂 *(Suggested new file:* `Routing Table & Path Selection.md`*)*

---

## 4. Branch 3: Static Routing

```
STATIC ROUTING (Admin Types Paths By Hand)
   │
   ├── How: manually configured by the network admin
   │
   ├── Types:
   │      ├── Standard Static ──> route to a specific network
   │      ├── Default Route   ──> "gateway of last resort" (0.0.0.0/0)
   │      └── Floating Static ──> backup route (higher AD)
   │
   ├── Pros: precise · secure · zero protocol overhead
   ├── Cons: no auto-adapting · hard to scale
   └── Use:  small networks · stub networks · backup paths
```

- **Like a hand-drawn paper map:** perfectly accurate for what you drew, but **you** must redraw it whenever a road changes.

> 📂 **Full guide:** [Static routing](./Static%20routing.md)

---

## 5. Branch 4: Dynamic Routing (with Protocols)

```
DYNAMIC ROUTING (Routers Learn Paths Automatically)
   │
   ├── How: routers TALK to each other & share what they know
   │
   ├── Two Big Families:
   │      │
   │      ├── IGP (Interior Gateway Protocols) ── INSIDE one organization
   │      │      │
   │      │      ├── Distance-Vector ("by rumor / hop count")
   │      │      │      ├── RIP   ──> simple, hop-count based (max 15)
   │      │      │      └── EIGRP ──> Cisco advanced, fast, uses metrics
   │      │      │
   │      │      └── Link-State ("full map of the network")
   │      │             └── OSPF  ──> open standard, builds a topology map
   │      │
   │      └── EGP (Exterior Gateway Protocol) ── BETWEEN organizations
   │             └── BGP   ──> the routing protocol of the INTERNET
   │
   ├── Pros: auto-adapts to failures · scales to huge networks
   ├── Cons: uses CPU + bandwidth · more complex to design
   └── Use:  medium-to-large networks · the internet
```

**Quick protocol comparison:**

| Protocol | Family | Type | Metric | Best For |
|----------|--------|------|--------|----------|
| **RIP** | IGP | Distance-Vector | Hop count | Tiny/simple networks |
| **EIGRP** | IGP | Advanced D-V | Bandwidth + delay | Cisco enterprise |
| **OSPF** | IGP | Link-State | Cost (bandwidth) | Large multi-vendor |
| **BGP** | EGP | Path-Vector | Path attributes | Internet / ISPs |

- **Like a live GPS:** routers constantly share traffic and road updates, so the system **reroutes automatically** when a path goes down.

> 📂 **Full guide:** [Dynamic Routing](./Dynamic%20Routing.md)
> 📝 *(Suggested future files: `RIP.md`, `OSPF.md`, `EIGRP.md`, `BGP.md`)*

---

## 6. Decision Flow: How a Router Handles a Packet

The **step-by-step logic** a router runs the instant a packet arrives:

```
            ┌──────────────────────────┐
            │ PACKET ARRIVES AT ROUTER  │
            └────────────┬─────────────┘
                         │
                         ▼
            ┌──────────────────────────┐
            │   Read DESTINATION IP     │
            └────────────┬─────────────┘
                         │
                         ▼
              ╔═══════════════════════╗
              ║ Match in routing table?║
              ╚═══════╦═══════════╦════╝
                 YES  │           │ NO
                      ▼           ▼
        ┌────────────────────┐ ┌──────────────────────┐
        │ MULTIPLE matches?   │ │ Default route exist?  │
        └──────┬──────────┬──┘ └──────┬──────────┬──────┘
            YES│          │NO      YES│          │NO
               ▼          │           ▼          ▼
   ┌────────────────────┐ │   ┌────────────┐ ┌────────────┐
   │ Apply tie-breakers: │ │   │ Send to     │ │ DROP packet │
   │ 1. Longest Prefix   │ │   │ "gateway of │ │ + send ICMP │
   │ 2. Admin Distance   │ │   │  last resort"│ │ unreachable │
   │ 3. Metric           │ │   └────────────┘ └────────────┘
   └─────────┬──────────┘ │
             │            │
             └─────┬──────┘
                   ▼
        ┌────────────────────────┐
        │ Decrement TTL           │
        │ Find Next Hop MAC (ARP) │
        │ FORWARD out interface   │
        └────────────────────────┘
```

**The 3 tie-breakers (in order):**
- 📏 **Longest Prefix Match** → most specific route wins **first**.
- ⚖️ **Administrative Distance** → breaks ties between **different sources** (static vs OSPF).
- 🎯 **Metric** → breaks ties **within the same protocol** (lowest cost wins).

---

## 7. Suggested Folder Structure

To grow your routing notes the same clean way you did for switching, here's a recommended layout:

```
└───routing layer 3
    │   README.md                           ← (was "1. Introduction.md")
    │   Static routing.md
    │   Dynamic Routing.md
    │   Routing Table & Path Selection.md   ← NEW (LPM, AD, Metric)
    │   Routing-Overview.md                 ← NEW (this flowchart)
    │
    └───dynamic protocols                   ← NEW folder
            RIP.md
            OSPF.md
            EIGRP.md
            BGP.md
```

**Suggested links** (for your master README.md and routing README.md):

```markdown
| **Routing core** | [Introduction](./README.md) · [Routing Table & Path Selection](./Routing%20Table%20&%20Path%20Selection.md) |
| **Static** | [Static routing](./Static%20routing.md) |
| **Dynamic** | [Dynamic Routing](./Dynamic%20Routing.md) |
| **Protocols** | [RIP](./dynamic%20protocols/RIP.md) · [OSPF](./dynamic%20protocols/OSPF.md) · [EIGRP](./dynamic%20protocols/EIGRP.md) · [BGP](./dynamic%20protocols/BGP.md) |
```

---

**One-line summary:** A router reads the **destination IP**, finds the **most specific matching route** (filled **manually = static** or **automatically = dynamic** via RIP/OSPF/EIGRP/BGP), breaks ties with **LPM → AD → Metric**, then forwards toward the next hop — or drops the packet if no path exists.

---