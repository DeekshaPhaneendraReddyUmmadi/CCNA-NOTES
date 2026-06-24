# Switching — Master Flowchart

### Index
1. [The Complete Switching Hierarchy](#1-the-complete-switching-hierarchy)
2. [Branch 1: Switch Basics](#2-branch-1-switch-basics)
3. [Branch 2: Core Concepts](#3-branch-2-core-concepts)
4. [Branch 3: Spanning Tree Protocol (STP)](#4-branch-3-spanning-tree-protocol-stp)
5. [Decision Flow: How a Switch Handles a Frame](#5-decision-flow-how-a-switch-handles-a-frame)

---

## 1. The Complete Switching Hierarchy

Here's how **everything in Layer 2 switching** fits together under one roof:

```
                          LAYER 2 SWITCHING
                       (Connecting local devices)
                                  │
        ┌─────────────────┬───────┴───────┬──────────────────┐
        │                 │               │                  │
   SWITCH BASICS     CORE CONCEPTS    SPANNING TREE      LAYER 3
   (the foundation)  (organizing it)   PROTOCOL (STP)    (routing brain)
        │                 │               │                  │
        │                 │               │                  └─> SVI / ip routing
        │                 │               │
   ┌────┴─────┐      ┌────┴────┬──────┐   └──> (see STP branch below)
   │          │      │         │      │
 MAC/CAM   Headers  VLANs   Trunking  EtherChannel
 Table    (frames)
   │
   ├─ Learning
   ├─ Forwarding
   └─ Flooding
```

---

## 2. Branch 1: Switch Basics

```
SWITCH BASICS (The Foundation)
   │
   ├── Ethernet Frame ──> Header (Dest MAC + Source MAC)
   │
   ├── MAC / CAM Table ──> The switch's "notebook"
   │
   ├── The 3 Rules:
   │      ├── Learning   (record source MAC → port)
   │      ├── Forwarding (send to known port)
   │      └── Flooding   (unknown? send to all)
   │
   └── Switching Methods:
          ├── Store-and-Forward (careful reader)
          ├── Cut-Through       (speedster)
          └── Fragment-Free     (middle ground)
```

- **Like a mailroom clerk:** reads the label (header), checks the directory (CAM table), and decides how carefully to deliver (switching method).

---

## 3. Branch 2: Core Concepts

```
CORE CONCEPTS (Organizing & Scaling Traffic)
   │
   ├── VLANs ──────────> Slice 1 switch into many virtual networks
   │      ├── Access Port  (one VLAN, to a PC)
   │      └── VLAN ID      (the group label)
   │
   ├── Trunking ───────> Carry MANY VLANs over ONE link
   │      ├── 802.1Q Tag   (the floor badge)
   │      ├── Native VLAN  (untagged lane)
   │      └── Allowed List (the guest list)
   │
   └── EtherChannel ───> Bundle MANY cables into ONE logical link
          ├── LACP (open standard)
          ├── PAgP (Cisco)
          └── Static "on"
```

- **Like an office building:** VLANs are the **floors**, trunks are the **shared elevators**, and EtherChannel is **widening the hallway** into a superhighway.

---

## 4. Branch 3: Spanning Tree Protocol (STP)

```
                    SPANNING TREE PROTOCOL (STP)
                    (The loop-prevention boss)
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
   SPEED FEATURES       PROTECTION FEATURES     CORE CONCEPTS
   (make ports fast)     (keep STP safe)        (VLANs, Trunks,
        │                     │                  EtherChannel)
        │                     │
     PortFast          ┌──────┴────────────┐
                       │                   │
                  EDGE-PORT           SWITCH-PORT
                  GUARDS              GUARDS
                       │                   │
              ┌────────┴───┐         ┌─────┴───────┐
              │            │         │             │
         BPDU Guard   BPDU Filter  Root Guard  Loop Guard
```

| Feature | Port Type | Trigger | Action | Recovery |
|---------|-----------|---------|--------|----------|
| **PortFast** | Edge | Link up | Skip to Forwarding | N/A |
| **BPDU Guard** | Edge | Any BPDU | Err-disable | Manual / timer |
| **BPDU Filter** | Edge | BPDUs present | Ignore | N/A |
| **Root Guard** | Switch | Superior BPDU | Block (root-incons.) | Automatic |
| **Loop Guard** | Switch | Missing BPDU | Block (loop-incons.) | Automatic |

---

## 5. Decision Flow: How a Switch Handles a Frame

This is the **step-by-step logic** a switch follows the moment a frame arrives:

```
            ┌─────────────────────────┐
            │   FRAME ARRIVES ON PORT │
            └────────────┬────────────┘
                         │
                         ▼
            ┌──────────────────────────┐
            │  Read SOURCE MAC + learn │  ← Learning
            │  it against this port    │
            └────────────┬─────────────┘
                         │
                         ▼
            ┌──────────────────────────┐
            │  Read DESTINATION MAC    │
            └────────────┬─────────────┘
                         │
                         ▼
              ╔══════════════════════╗
              ║ Is Dest MAC in the   ║
              ║ CAM table?           ║
              ╚═══════╦══════════╦═══╝
                 YES  │          │ NO
                      ▼          ▼
        ┌──────────────────┐  ┌───────────────────────┐
        │ FORWARD out the  │  │ FLOOD out ALL ports   │
        │ matching port    │  │ except the source     │
        └────────┬─────────┘  └───────────┬───────────┘
                 │                        │
                 ▼                        ▼
        ┌──────────────────┐        ┌────────────────────────┐   
        │ Check FCS for    │        |(Destination replies,   |
        │ errors → deliver │        |switch learns its MAC,  |
        └──────────────────┘        |future frames forwarded)|
                                    └────────────────────────┘   
```

**Special checks that wrap around this flow:**
- 🏷️ **VLAN check:** Is the frame in the right VLAN? Tagged frames (trunks) keep VLANs separate.
- 🌳 **STP check:** Is the port in a **Forwarding** state, or **blocked** to prevent a loop?
- 🛡️ **Guard check:** Did a guard (BPDU/Root/Loop) trip and block/disable the port?

---

**One-line summary:** A switch **learns** who's where, **forwards** to known devices (or **floods** to find unknown ones), while **VLANs/trunks** keep traffic organized, **EtherChannel** adds speed, and **STP + its guards** keep the whole thing loop-free and safe.

---
