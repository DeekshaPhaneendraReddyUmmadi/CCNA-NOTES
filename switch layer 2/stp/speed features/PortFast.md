# PortFast

### Index
1. [What is PortFast?](#1-what-is-portfast)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: Edge Port Designation](#4-key-component-edge-port-designation)
5. [Key Component: STP State Skipping](#5-key-component-stp-state-skipping)
6. [Key Component: BPDU Monitoring](#6-key-component-bpdu-monitoring)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created PortFast](#8-who-created-portfast)
9. [Types / Variations of PortFast](#9-types--variations-of-portfast)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is PortFast?

- **PortFast** is a Spanning Tree feature that lets a switch port **skip the slow startup steps** and jump straight to **forwarding traffic**.
- It's used only on ports connected to **end devices** (PCs, servers, printers) — never to other switches.
- Its primary goal: to make devices **connect and work instantly** instead of waiting ~30 seconds.

```
PortFast (Edge Ports Only)
   │
   ├──> Skips Listening + Learning states
   ├──> Goes straight to Forwarding
   └──> MUST be paired with a guard for safety
              │
              └──> BPDU Guard (the safety net)

```

---

## 2. Why do we need it? (The Problem it Solves)

- Normally, when a device plugs in, Spanning Tree makes the port wait through **Listening and Learning** states (about **30 seconds**) before passing traffic.
- During that wait, the device can **fail to get an IP address (DHCP)** or appear "disconnected."
- PortFast solves the **"why is my PC taking so long to connect?" problem** by allowing instant access on end-device ports.

---

## 3. How it relates to the broader network

- PortFast is a feature **of Spanning Tree Protocol (STP)** — it changes how STP treats specific ports.
- It works closely with **BPDU Guard** and **BPDU Filter**, which protect these fast ports from accidental loops.
- **Example:** Think of PortFast like a **VIP fast-pass at an airport**. Trusted, pre-screened guests (end devices) skip the long security line (STP states) and walk straight to the gate — but only people who clearly aren't a threat get the pass.

---

## 4. Key Component: Edge Port Designation

* **What it is:** Marking a port as an **"edge port"** — one that connects to a single end device, not a switch.
* **Why it matters:** Only edge ports are safe to fast-forward without risking loops.
* **Example:** Like labeling a door **"Staff Only — Direct Entry"** because you know exactly who uses it.

---

## 5. Key Component: STP State Skipping

* **What it is:** The act of bypassing the **Listening** and **Learning** states, going straight to **Forwarding**.
* **Why it matters:** It removes the ~30-second delay for end users.
* **Example:** Like a **pre-approved express lane** at a checkpoint — no waiting, straight through.

---

## 6. Key Component: BPDU Monitoring

* **What it is:** Watching for **BPDUs** (Spanning Tree "hello" messages) arriving on a PortFast port.
* **Why it matters:** If a BPDU appears, it means a **switch was wrongly plugged in** — a loop risk that must be caught.
* **Example:** Like a **smoke detector in a no-smoking room** — it instantly alerts if something that shouldn't be there shows up.

---

## 7. Safety & Security Features

* **What it is:** **BPDU Guard** (shuts a PortFast port down if it receives a BPDU) and **BPDU Filter** (ignores BPDUs on that port).
* **Why it matters:** Because PortFast skips loop protection, these guards stop a misplugged switch from causing a **network-wide loop**.
* **Example:** Like a **fast-pass that instantly self-destructs** if the holder tries to bring in forbidden items — protecting everyone behind them.

---

## 8. Who created PortFast

* **Cisco Systems**, as an enhancement to standard Spanning Tree.

    **Quick facts:**
    * **Role:** Developer of the PortFast STP enhancement.
    * **Invention/Creation:** PortFast, to remove STP delays on end-device ports.
    * **Purpose:** To let user devices connect and pass traffic instantly.
    * **Recognition/Standard:** The concept maps to **"Edge Ports"** in the IEEE **802.1w (RSTP)** standard.

---

## 9. Types / Variations of PortFast

- **Access PortFast**
- **Trunk PortFast**
- **PortFast Default (Global)**

#### Access PortFast
* **What it is:** PortFast enabled on a single access (end-device) port.
* **Why use it:** The standard, safest use for ports connecting PCs or printers.
* **Example:** Like a **single express lane** opened just for a known regular visitor.

#### Trunk PortFast
* **What it is:** PortFast enabled on a trunk port connecting to a **non-switch device** (like a server with trunking).
* **Why use it:** For trunked end devices (e.g., virtualization hosts) that need fast startup.
* **Example:** Like a **VIP entrance for a delivery truck** carrying multiple labeled goods.

#### PortFast Default (Global)
* **What it is:** Enables PortFast automatically on **all access ports** switch-wide.
* **Why use it:** To save time configuring many end-device ports at once.
* **Example:** Like making **express lanes the default** for an entire known-safe terminal.

---

## 10. Flow of Phases / How it Works

### Phase 1: Port Comes Up
* **The Action:** An end device is plugged into a PortFast-enabled port.
* **The Logic:** The switch sees the link go active and checks it's marked as PortFast.
* **The Result:** The port is flagged to skip the normal STP wait.

### Phase 2: State Skipping
* **The Action:** The port bypasses **Listening** and **Learning**.
* **The Logic:** Since it's an edge port, STP trusts it won't create a loop.
* **The Result:** The port enters **Forwarding** almost instantly.

### Phase 3: Active Monitoring
* **The Action:** The switch continuously watches for any incoming **BPDU**.
* **The Logic:** A BPDU means a switch (not an end device) is connected — a violation.
* **The Result:** Normal traffic flows; the port stays alert for trouble.

### Phase 4: Protection Trigger (If Violated)
* **The Action:** If a BPDU arrives and **BPDU Guard** is on, the port is shut down (err-disabled).
* **The Logic:** Better to disable one port than risk a loop taking down the whole network.
* **The Result:** The loop threat is **instantly neutralized**.

**Example in the Field:** Picture an **airport VIP fast-pass lane**. A trusted traveler scans their pass and walks straight to the gate (state skipping). Security cameras keep watching (monitoring). If that traveler suddenly tries to bring in something forbidden — like a switch where a PC should be — the gate **slams shut immediately** (BPDU Guard), protecting the whole terminal.

---

## 11. States and Timers

- **Forwarding State:** PortFast ports go here immediately on link-up.
- **Err-Disabled State:** Where a port lands if BPDU Guard is triggered.
- **No 15s Listening / Learning Delay:** PortFast skips the two **15-second** STP timers (saving ~30 seconds total).
- **Errdisable Recovery Timer:** Optional auto-recovery (default **300 seconds**) to bring an err-disabled port back automatically.

---

## 12. Advanced / Extra Features

- **BPDU Guard:** Auto-shuts down a PortFast port that wrongly receives a BPDU.
- **BPDU Filter:** Suppresses BPDUs on PortFast ports (use with caution — can mask loops).
- **Errdisable Recovery:** Automatically re-enables shut-down ports after a set time.
- **PortFast for Trunks:** Extends fast-forwarding to trunked end-host ports.
- **Integration with RSTP:** In Rapid STP, PortFast ports are simply called **Edge Ports** and converge fast natively.

---

## 13. Related Commands & Configuration

* **Command:** `spanning-tree portfast`
* **What it does:** Enables PortFast on the selected access interface.
* **When to use it:** **Setup** — on ports connecting end devices like PCs or printers.

---

* **Command:** `spanning-tree portfast trunk`
* **What it does:** Enables PortFast on a trunk port (for non-switch trunked devices).
* **When to use it:** **Setup** — for servers or hosts using trunking.

---

* **Command:** `spanning-tree portfast default`
* **What it does:** Globally enables PortFast on **all access ports** by default.
* **When to use it:** **Setup** — to save time on switches with many end-device ports.

---

* **Command:** `spanning-tree bpduguard enable`
* **What it does:** Turns on BPDU Guard for the interface, shutting it down if a BPDU arrives.
* **When to use it:** **Setup/security** — strongly recommended alongside PortFast.

---

* **Command:** `spanning-tree portfast bpduguard default`
* **What it does:** Globally enables BPDU Guard on all PortFast-enabled ports.
* **When to use it:** **Setup/security** — a best-practice safety net switch-wide.

---

* **Command:** `spanning-tree bpdufilter enable`
* **What it does:** Filters (ignores) BPDUs on the interface.
* **When to use it:** **Setup** — use cautiously; it can hide loops if misapplied.

---

* **Command:** `errdisable recovery cause bpduguard`
* **What it does:** Allows ports shut down by BPDU Guard to recover automatically.
* **When to use it:** **Setup/troubleshooting** — to avoid manual re-enabling.

---

* **Command:** `show spanning-tree interface [port] portfast`
* **What it does:** Shows whether PortFast is enabled on a specific port.
* **When to use it:** **Verifying** — to confirm PortFast status.

---

* **Command:** `show spanning-tree summary`
* **What it does:** Displays global STP settings, including PortFast and BPDU Guard defaults.
* **When to use it:** **Verifying** — to review switch-wide STP configuration.

---

* **Command:** `show interfaces status err-disabled`
* **What it does:** Lists ports currently in the err-disabled state.
* **When to use it:** **Troubleshooting** — to find ports shut down by BPDU Guard.

---
