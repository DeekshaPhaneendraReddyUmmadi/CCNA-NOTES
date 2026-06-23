# Root Guard

### Index
1. [What is Root Guard?](#1-what-is-root-guard)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: Root Bridge Awareness](#4-key-component-root-bridge-awareness)
5. [Key Component: Superior BPDU Detection](#5-key-component-superior-bpdu-detection)
6. [Key Component: Root-Inconsistent State](#6-key-component-root-inconsistent-state)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created Root Guard](#8-who-created-root-guard)
9. [Types / Variations of Root Guard](#9-types--variations-of-root-guard)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is Root Guard?

- **Root Guard** is a Spanning Tree feature that **protects which switch is the "root bridge"** (the boss of the Spanning Tree).
- It stops a port from accepting any switch that tries to **claim the root role** when it shouldn't.
- Its primary goal: to **keep the root bridge stable and in the right place**, exactly where the network designer intended.

---

## 2. Why do we need it? (The Problem it Solves)

- In Spanning Tree, the switch with the **best (lowest) priority** becomes the root — the central decision-maker for all paths.
- If a **rogue or misconfigured switch** announces a better priority, it can **steal the root role**, forcing traffic down slow or unexpected paths.
- Root Guard solves the **"wrong switch took over as boss" problem** by refusing any takeover attempt on protected ports.

---

## 3. How it relates to the broader network

- Root Guard is part of **Spanning Tree Protocol (STP)** and is applied on **switch-to-switch ports** (unlike BPDU Guard, which is for edge ports).
- It ensures the **traffic flow and topology** stay optimized around the intended root.
- **Example:** Think of Root Guard like a **company rule that the CEO must stay at headquarters**. If a branch office suddenly tries to declare itself the new headquarters, Root Guard **blocks that claim** — the real HQ keeps running things from the right place.

---

## 4. Key Component: Root Bridge Awareness

* **What it is:** The switch knows which device **should** be the root and protects that arrangement.
* **Why it matters:** It keeps the network's "decision center" predictable and well-placed.
* **Example:** Like a **building directory that always lists the correct head office** — everyone knows where the boss sits.

---

## 5. Key Component: Superior BPDU Detection

* **What it is:** Watching for a **"superior BPDU"** — a message claiming a better (lower) priority than the current root.
* **Why it matters:** A superior BPDU on the wrong port signals an attempted **root takeover**.
* **Example:** Like a **fraud detector** that flags anyone claiming to outrank the real CEO.

---

## 6. Key Component: Root-Inconsistent State

* **What it is:** The special **blocking state** a port enters when it hears a superior BPDU.
* **Why it matters:** It stops the rogue switch's traffic while **keeping the port physically up** — ready to recover automatically.
* **Example:** Like putting an over-eager employee **"on hold"** — not fired, just paused until they behave correctly.

---

## 7. Safety & Security Features

* **What it is:** Automatic blocking of takeover attempts plus **self-healing recovery** — the port unblocks on its own once the superior BPDUs stop.
* **Why it matters:** It defends the topology from **rogue switches and misconfigurations** without needing an admin to manually fix it.
* **Example:** Like a **security gate that locks during a threat and reopens automatically** once the danger passes — no guard needed to reset it.

---

## 8. Who created Root Guard

* **Cisco Systems**, as a Spanning Tree topology-protection enhancement.

    **Quick facts:**
    * **Role:** Developer of the Root Guard STP feature.
    * **Invention/Creation:** Root Guard, to lock the root bridge in its intended position.
    * **Purpose:** To prevent unauthorized switches from becoming the root bridge.
    * **Recognition/Standard:** A Cisco enhancement built on the IEEE **802.1D / 802.1w** STP framework.

---

## 9. Types / Variations of Root Guard

- **Per-Port Root Guard**

(Note: Root Guard is configured **per-interface only** — there is no global "default" version like BPDU Guard. It is intentionally applied to specific switch-facing ports.)

#### Per-Port Root Guard
* **What it is:** Root Guard enabled on a **specific port** facing a downstream/neighbor switch.
* **Why use it:** To protect the root role only where takeovers might come from — typically links toward access or edge switches.
* **Example:** Like posting a **guard only at the doors that lead to the boss's office**, not every door.

---

## 10. Flow of Phases / How it Works

### Phase 1: Normal Operation
* **The Action:** A Root Guard port forwards traffic and exchanges normal BPDUs.
* **The Logic:** As long as no switch claims a better root, everything runs as designed.
* **The Result:** The intended root bridge stays in charge; traffic flows normally.

### Phase 2: Superior BPDU Arrives
* **The Action:** The port receives a BPDU claiming a **better (lower) priority** than the current root.
* **The Logic:** Root Guard recognizes this as an attempted **root takeover**.
* **The Result:** A violation is flagged immediately.

### Phase 3: Port Blocking
* **The Action:** The switch moves the port into the **root-inconsistent (blocking)** state.
* **The Logic:** Blocking the port prevents the rogue switch from becoming root.
* **The Result:** The takeover is stopped; the real root keeps its position.

### Phase 4: Automatic Recovery
* **The Action:** The port keeps listening; once the **superior BPDUs stop**, it unblocks itself.
* **The Logic:** No more takeover attempts means it's safe to forward again.
* **The Result:** The port returns to normal **automatically** — no manual reset needed.

**Example in the Field:** Picture a **company with a fixed head office**. Everything runs smoothly (normal operation). One day, a branch tries to declare itself the new HQ (superior BPDU). Root Guard **freezes that branch's authority** (port blocking), so the real HQ stays in control. Once the branch stops making the false claim, it's **welcomed back into normal operation automatically** (recovery).

---

## 11. States and Timers

- **Forwarding State:** Normal state while no superior BPDU is heard.
- **Root-Inconsistent (Blocking) State:** Where the port lands when a superior BPDU is detected.
- **Recovery Timing:** The port unblocks roughly after the **Max Age timer (default 20 seconds)** passes without further superior BPDUs.
- **No Permanent Shutdown:** Unlike BPDU Guard's err-disable, Root Guard **never disables the port** — it only blocks and self-heals.

---

## 12. Advanced / Extra Features

- **Self-Healing Behavior:** Automatically recovers without admin action — a key difference from BPDU Guard.
- **Topology Protection:** Keeps traffic on the **optimal designed paths** by locking the root location.
- **Pairs with Loop Guard:** Loop Guard protects against unexpected loss of BPDUs, while Root Guard protects against unwanted root changes — complementary defenses.
- **Logging & Syslog Alerts:** Generates messages when a port goes root-inconsistent, helping admins spot rogue switches.
- **Common Placement:** Best on ports facing **downstream/access switches**, never on the path toward the legitimate root.

---

## 13. Related Commands & Configuration

* **Command:** `spanning-tree guard root`
* **What it does:** Enables Root Guard on the selected interface.
* **When to use it:** **Setup/security** — on ports facing switches that should never become root.

---

* **Command:** `no spanning-tree guard root`
* **What it does:** Disables Root Guard on the interface.
* **When to use it:** **Setup** — to remove protection from a port (e.g., the legitimate root path).

---

* **Command:** `show spanning-tree inconsistentports`
* **What it does:** Lists all ports currently in a root-inconsistent (blocked) state.
* **When to use it:** **Troubleshooting** — to find ports that blocked a root takeover attempt.

---

* **Command:** `show spanning-tree interface [port] detail`
* **What it does:** Shows detailed STP info for a port, including Root Guard status.
* **When to use it:** **Verifying** — to confirm Root Guard is active on a specific port.

---

* **Command:** `show spanning-tree summary`
* **What it does:** Displays overall STP settings and protection feature status.
* **When to use it:** **Verifying** — to review switch-wide STP configuration.

---

* **Command:** `show spanning-tree root`
* **What it does:** Displays the current root bridge and root port information.
* **When to use it:** **Verifying & troubleshooting** — to confirm the correct switch is root.

---

* **Command:** `show spanning-tree vlan [id]`
* **What it does:** Shows the STP topology and port roles for a specific VLAN.
* **When to use it:** **Troubleshooting** — to check root placement and port states per VLAN.

---

**Quick comparison tip:** Remember the difference between the STP guards —
- **BPDU Guard** → **edge/PortFast ports** → **err-disables** the port (manual/timer recovery).
- **Root Guard** → **switch-facing ports** → **blocks (root-inconsistent)** → **self-recovers** automatically.

---
