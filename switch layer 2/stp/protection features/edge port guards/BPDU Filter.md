# BPDU Filter

### Index
1. [What is BPDU Filter?](#1-what-is-bpdu-filter)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: BPDU Suppression (Sending)](#4-key-component-bpdu-suppression-sending)
5. [Key Component: BPDU Ignoring (Receiving)](#5-key-component-bpdu-ignoring-receiving)
6. [Key Component: Interface vs Global Behavior](#6-key-component-interface-vs-global-behavior)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created BPDU Filter](#8-who-created-bpdu-filter)
9. [Types / Variations of BPDU Filter](#9-types--variations-of-bpdu-filter)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is BPDU Filter?

- **BPDU Filter** is a Spanning Tree feature that **stops BPDUs** (Spanning Tree "hello" messages) from being **sent and/or received** on a port.
- It's applied to **PortFast edge ports** where you don't want Spanning Tree "chatter."
- Its primary goal: to keep BPDUs from flowing on edge ports — but it must be used **very carefully**, because it can hide dangerous loops.

---

## 2. Why do we need it? (The Problem it Solves)

- On edge ports, switches still send BPDUs out by default — which is usually **unnecessary noise** toward an end device.
- In some special cases (like handing a port to a customer or a separate STP domain), you **don't want your BPDUs leaking out** or theirs coming in.
- BPDU Filter solves the **"unwanted Spanning Tree traffic on edge ports" problem** by suppressing those messages.

---

## 3. How it relates to the broader network

- BPDU Filter is part of **Spanning Tree Protocol (STP)** and pairs with **PortFast**.
- It is the **opposite philosophy** of BPDU Guard: instead of shutting a port down when BPDUs appear, it simply **ignores or suppresses** them.
- **Example:** Think of BPDU Filter like **putting tape over a doorbell**. BPDU Guard would *lock the door* if someone rings; BPDU Filter just makes the bell **silent** — convenient, but if a real visitor (a rogue switch) arrives, you might **not notice the danger**.

---

## 4. Key Component: BPDU Suppression (Sending)

* **What it is:** Stopping the switch from **sending** BPDUs out of the port.
* **Why it matters:** It prevents your Spanning Tree info from leaking to devices that shouldn't see it.
* **Example:** Like choosing **not to announce your meeting schedule** to outsiders.

---

## 5. Key Component: BPDU Ignoring (Receiving)

* **What it is:** Making the switch **drop/ignore** any BPDUs it receives on the port.
* **Why it matters:** It keeps outside Spanning Tree messages from affecting your topology.
* **Example:** Like **refusing to read junk mail** from another company.

---

## 6. Key Component: Interface vs Global Behavior

* **What it is:** BPDU Filter behaves **differently** depending on whether it's set globally or per-interface (this is the most important — and dangerous — detail).
* **Why it matters:** The two modes have **very different safety levels**, and confusing them causes outages.
* **Example:** Like two switches that **look identical but do opposite things** — you must know which one you're flipping.

---

## 7. Safety & Security Features

* **What it is:** The **global mode** is safer — it sends a few BPDUs first, and if it hears any back, it **automatically disables filtering** (falling back to PortFast/BPDU Guard behavior). The **interface mode** is riskier — it filters **unconditionally**, with **no safety check**.
* **Why it matters:** Interface-mode filtering can **completely disable loop protection** on a port, allowing a silent loop if a switch is plugged in.
* **Example:** Like the difference between a **smart lock that checks for intruders first** (global) versus **unplugging the alarm entirely** (interface) — the second is fast but dangerous.

---

## 8. Who created BPDU Filter

* **Cisco Systems**, as a Spanning Tree edge-port enhancement.

    **Quick facts:**
    * **Role:** Developer of the BPDU Filter STP feature.
    * **Invention/Creation:** BPDU Filter, to control BPDU flow on PortFast edge ports.
    * **Purpose:** To suppress or ignore BPDUs where Spanning Tree chatter is unwanted.
    * **Recognition/Standard:** A Cisco enhancement built on the IEEE **802.1D / 802.1w** STP framework.

---

## 9. Types / Variations of BPDU Filter

- **Global BPDU Filter (Safe Mode)**
- **Interface BPDU Filter (Unconditional Mode)**

#### Global BPDU Filter (Safe Mode)
* **What it is:** Enabled switch-wide on all PortFast ports; sends a few BPDUs, then filters — but **reverts to normal if a BPDU is received**.
* **Why use it:** To reduce BPDU noise on edge ports **while keeping a safety net**.
* **Example:** Like a **silent doorbell that still flashes a warning light** if a stranger keeps knocking.

#### Interface BPDU Filter (Unconditional Mode)
* **What it is:** Enabled on a specific port; **completely** stops sending and ignores receiving — **no safety fallback**.
* **Why use it:** Only in **tightly controlled, expert situations** (e.g., service-provider edges) where you fully understand the risk.
* **Example:** Like **fully disconnecting the alarm** for one door — only acceptable if you're certain it's safe.

---

## 10. Flow of Phases / How it Works

### Phase 1: Filter Activation
* **The Action:** BPDU Filter is enabled on a PortFast port (globally or per-interface).
* **The Logic:** The switch decides whether to apply the **safe (global)** or **unconditional (interface)** behavior.
* **The Result:** BPDU handling on that port changes accordingly.

### Phase 2: Global Mode — Initial Check
* **The Action:** In global mode, the port sends a **handful of BPDUs** at link-up.
* **The Logic:** If **no BPDU comes back**, it's a true edge port — filtering continues.
* **The Result:** BPDUs are suppressed safely on a confirmed edge port.

### Phase 3: Global Mode — Safety Fallback
* **The Action:** If a **BPDU is received**, filtering is **automatically dropped**.
* **The Logic:** A received BPDU means a switch is connected — loop protection must resume.
* **The Result:** The port reverts to normal PortFast/BPDU Guard behavior — **loop avoided**.

### Phase 4: Interface Mode — No Fallback
* **The Action:** In interface mode, the port **always** filters, ignoring all BPDUs.
* **The Logic:** No checking is done — the admin has taken full responsibility.
* **The Result:** Convenient, but **a loop can form undetected** if misused.

**Example in the Field:** Picture a **reception desk that mutes its phone**. In **smart mode (global)**, if the same caller keeps ringing (a switch sending BPDUs), the desk **un-mutes to handle it** (safety fallback). In **manual mode (interface)**, the phone is **ripped off the hook entirely** — quiet, but if an emergency call comes in (a loop), **nobody hears it**.

---

## 11. States and Timers

- **Forwarding State:** BPDU-filtered PortFast ports forward traffic normally.
- **No Err-Disable:** Unlike BPDU Guard, BPDU Filter does **not** shut the port down.
- **Initial BPDU Burst (Global Mode):** A small number of BPDUs (around 10) are sent at link-up before filtering settles in.
- **Automatic Fallback (Global Mode Only):** Reverts instantly upon receiving a BPDU — interface mode has **no timer or fallback**.

---

## 12. Advanced / Extra Features

- **Global vs Interface Distinction:** The single most important concept — global is conditional/safe, interface is unconditional/risky.
- **Service-Provider Edge Use:** Often used at boundaries between separate STP domains to keep them independent.
- **Interaction with BPDU Guard:** On the same port, BPDU Filter (interface mode) effectively **overrides** BPDU Guard — a common misconfiguration trap.
- **Loop Risk Warning:** Interface mode can silently disable loop protection — always document and justify its use.
- **Best Practice:** Prefer **BPDU Guard** for edge security; reserve BPDU Filter for **specific, well-understood** scenarios.

---

## 13. Related Commands & Configuration

* **Command:** `spanning-tree portfast bpdufilter default`
* **What it does:** Globally enables BPDU Filter (safe mode) on all PortFast ports.
* **When to use it:** **Setup** — to reduce BPDU noise switch-wide while keeping the safety fallback.

---

* **Command:** `spanning-tree bpdufilter enable`
* **What it does:** Enables BPDU Filter (unconditional mode) on a specific interface — **no safety fallback**.
* **When to use it:** **Setup** — only in expert, tightly controlled cases; use with caution.

---

* **Command:** `spanning-tree bpdufilter disable`
* **What it does:** Disables BPDU Filter on a specific interface (overrides the global default).
* **When to use it:** **Setup** — to ensure a port keeps normal BPDU handling.

---

* **Command:** `show spanning-tree summary`
* **What it does:** Displays global STP settings, including BPDU Filter default status.
* **When to use it:** **Verifying** — to confirm whether global filtering is on.

---

* **Command:** `show spanning-tree interface [port] detail`
* **What it does:** Shows detailed STP info for a port, including BPDU Filter status.
* **When to use it:** **Verifying** — to check filtering on a specific port.

---

* **Command:** `show spanning-tree interface [port]`
* **What it does:** Displays the port's STP role and state.
* **When to use it:** **Troubleshooting** — to confirm the port is behaving as expected.

---

**Quick comparison tip:** The full STP edge/switch protection family —
- **BPDU Guard** → edge ports → **unexpected BPDU** → **err-disables** the port.
- **BPDU Filter** → edge ports → **suppresses/ignores** BPDUs → **no shutdown** (global = safe, interface = risky).
- **Root Guard** → switch ports → **superior BPDU** → **root-inconsistent block**, self-recovers.
- **Loop Guard** → switch ports → **missing BPDU** → **loop-inconsistent block**, self-recovers.

---
