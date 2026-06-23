# Loop Guard

### Index
1. [What is Loop Guard?](#1-what-is-loop-guard)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: BPDU Reception Monitoring](#4-key-component-bpdu-reception-monitoring)
5. [Key Component: Loop-Inconsistent State](#5-key-component-loop-inconsistent-state)
6. [Key Component: Non-Designated Port Focus](#6-key-component-non-designated-port-focus)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created Loop Guard](#8-who-created-loop-guard)
9. [Types / Variations of Loop Guard](#9-types--variations-of-loop-guard)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is Loop Guard?

- **Loop Guard** is a Spanning Tree feature that protects against loops caused when a switch **suddenly stops receiving BPDUs** on a port that should keep getting them.
- Normally, a port that stops hearing BPDUs assumes the path is clear and starts forwarding — which can **accidentally open a loop**.
- Its primary goal: to **catch "silent" failures** and keep a blocked port blocked when something goes wrong.

---

## 2. Why do we need it? (The Problem it Solves)

- In STP, blocked ports stay blocked **because they keep hearing BPDUs** from a neighbor.
- If those BPDUs **stop arriving** (due to a faulty cable, software bug, or one-way link), the port wrongly thinks "the path is gone" and **starts forwarding** — creating a **loop**.
- Loop Guard solves the **"missing BPDU = false safe signal" problem** by keeping the port blocked instead of trusting silence.

---

## 3. How it relates to the broader network

- Loop Guard is part of **Spanning Tree Protocol (STP)** and works on **switch-to-switch links**, specifically on **non-designated ports** (root and alternate ports).
- It complements **Root Guard** — Root Guard stops *unwanted* BPDUs, while Loop Guard reacts to *missing* BPDUs.
- **Example:** Think of Loop Guard like a **night-shift security guard who must radio in every few minutes**. If the radio suddenly **goes silent**, the system doesn't assume "all clear" — it assumes **something is wrong** and keeps the door **locked** to be safe.

---

## 4. Key Component: BPDU Reception Monitoring

* **What it is:** Continuously checking that expected BPDUs **keep arriving** on a port.
* **Why it matters:** Missing BPDUs are the warning sign of a one-way or failed link.
* **Example:** Like a **heartbeat monitor** — as long as it beeps steadily, all is well; sudden silence is the alarm.

---

## 5. Key Component: Loop-Inconsistent State

* **What it is:** The special **blocking state** a port enters when its expected BPDUs stop arriving.
* **Why it matters:** It keeps the port **safely blocked** instead of letting it forward into a possible loop.
* **Example:** Like a **safety latch that stays locked** when the "all-clear" signal disappears.

---

## 6. Key Component: Non-Designated Port Focus

* **What it is:** Loop Guard is meant for **root ports and alternate (blocked) ports** — ports that *receive* BPDUs.
* **Why it matters:** These are exactly the ports where missing BPDUs cause dangerous loops.
* **Example:** Like guarding **the doors that are supposed to stay locked**, not the main entrance everyone uses.

---

## 7. Safety & Security Features

* **What it is:** Automatic blocking on BPDU loss, plus **self-healing recovery** — the port restores itself the moment BPDUs return.
* **Why it matters:** It defends against **hard-to-detect one-way link failures** that other STP checks can miss, with no manual fix needed.
* **Example:** Like a **fail-safe brake** that engages automatically when it loses contact and releases the instant control returns.

---

## 8. Who created Loop Guard

* **Cisco Systems**, as a Spanning Tree loop-prevention enhancement.

    **Quick facts:**
    * **Role:** Developer of the Loop Guard STP feature.
    * **Invention/Creation:** Loop Guard, to handle loops caused by unexpected BPDU loss.
    * **Purpose:** To keep non-designated ports blocked when BPDUs stop arriving.
    * **Recognition/Standard:** A Cisco enhancement built on the IEEE **802.1D / 802.1w** STP framework.

---

## 9. Types / Variations of Loop Guard

- **Per-Port Loop Guard**
- **Global (Default) Loop Guard**

#### Per-Port Loop Guard
* **What it is:** Loop Guard enabled on a **specific switch-facing port**.
* **Why use it:** For precise control on individual root/alternate ports.
* **Example:** Like a **single backup lock on one critical gate**.

#### Global (Default) Loop Guard
* **What it is:** Enabled switch-wide so **all eligible point-to-point ports** are protected.
* **Why use it:** A best-practice safety net across the whole switch.
* **Example:** Like installing **fail-safe locks on every secured door** at once.

---

## 10. Flow of Phases / How it Works

### Phase 1: Normal Operation
* **The Action:** A non-designated port keeps receiving BPDUs from its neighbor.
* **The Logic:** Steady BPDUs confirm the link is healthy and the port should stay blocked.
* **The Result:** The loop-free topology holds as designed.

### Phase 2: BPDUs Stop Arriving
* **The Action:** The port suddenly **stops receiving** expected BPDUs.
* **The Logic:** Loop Guard treats this silence as a **possible one-way link failure**, not an "all clear."
* **The Result:** A potential loop condition is flagged.

### Phase 3: Port Blocking
* **The Action:** The switch moves the port into the **loop-inconsistent (blocking)** state.
* **The Logic:** Keeping the port blocked prevents it from forwarding into a loop.
* **The Result:** The loop is **prevented before it forms**.

### Phase 4: Automatic Recovery
* **The Action:** Once BPDUs **start arriving again**, the port returns to normal.
* **The Logic:** Restored BPDUs mean the link is healthy again.
* **The Result:** The port recovers **automatically** — no admin action needed.

**Example in the Field:** Picture a **security guard who must radio HQ every minute**. As long as HQ hears the check-ins, all is normal (normal operation). Suddenly the radio goes silent (BPDUs stop). Instead of assuming everything's fine, HQ **keeps the vault locked** as a precaution (loop-inconsistent block). When the radio crackles back to life (BPDUs return), the vault **reopens automatically** (recovery).

---

## 11. States and Timers

- **Forwarding/Blocking (Normal):** Port operates per its STP role while BPDUs flow.
- **Loop-Inconsistent (Blocking) State:** Where the port lands when expected BPDUs stop.
- **Max Age Timer (default 20 seconds):** The window after which missing BPDUs trigger the loop-inconsistent state.
- **Automatic Recovery:** Happens the moment a valid BPDU is received again — no permanent shutdown.

---

## 12. Advanced / Extra Features

- **Self-Healing Behavior:** Recovers automatically when BPDUs return — like Root Guard, unlike BPDU Guard.
- **Point-to-Point Link Focus:** Works best on full-duplex point-to-point links between switches.
- **Pairs with UDLD:** **UniDirectional Link Detection** complements Loop Guard by detecting one-way fiber/cable failures at the physical layer.
- **Mutually Exclusive with Root Guard:** A single port should not run both — they serve different roles on different port types.
- **Per-VLAN Behavior:** In PVST+, Loop Guard can act on a per-VLAN basis on a trunk port.

---

## 13. Related Commands & Configuration

* **Command:** `spanning-tree guard loop`
* **What it does:** Enables Loop Guard on the selected interface.
* **When to use it:** **Setup/security** — on root or alternate ports facing other switches.

---

* **Command:** `spanning-tree loopguard default`
* **What it does:** Globally enables Loop Guard on all eligible point-to-point ports.
* **When to use it:** **Setup/security** — the recommended switch-wide best practice.

---

* **Command:** `no spanning-tree guard loop`
* **What it does:** Disables Loop Guard on a specific interface (overrides the global default).
* **When to use it:** **Setup** — for ports that legitimately need an exception.

---

* **Command:** `udld enable` (or `udld aggressive`)
* **What it does:** Enables UniDirectional Link Detection to catch one-way physical failures.
* **When to use it:** **Setup/security** — alongside Loop Guard for stronger fiber-link protection.

---

* **Command:** `show spanning-tree inconsistentports`
* **What it does:** Lists all ports currently in a loop-inconsistent (or root-inconsistent) state.
* **When to use it:** **Troubleshooting** — to find ports blocked by Loop Guard.

---

* **Command:** `show spanning-tree interface [port] detail`
* **What it does:** Shows detailed STP info for a port, including Loop Guard status.
* **When to use it:** **Verifying** — to confirm Loop Guard is active on a port.

---

* **Command:** `show spanning-tree summary`
* **What it does:** Displays global STP settings, including Loop Guard default status.
* **When to use it:** **Verifying** — to review switch-wide configuration.

---

**Quick comparison tip:** Remember the difference among the STP guards —
- **BPDU Guard** → **edge ports** → reacts to **unexpected BPDUs** → **err-disables**.
- **Root Guard** → **switch ports** → reacts to **superior BPDUs** → **blocks (root-inconsistent), self-recovers**.
- **Loop Guard** → **switch ports** → reacts to **missing BPDUs** → **blocks (loop-inconsistent), self-recovers**.

---