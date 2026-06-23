# BPDU Guard

### Index
1. [What is BPDU Guard?](#1-what-is-bpdu-guard)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component: BPDU Detection](#4-key-component-bpdu-detection)
5. [Key Component: Err-Disable Action](#5-key-component-err-disable-action)
6. [Key Component: PortFast Pairing](#6-key-component-portfast-pairing)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created BPDU Guard](#8-who-created-bpdu-guard)
9. [Types / Variations of BPDU Guard](#9-types--variations-of-bpdu-guard)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Related Commands & Configuration](#13-related-commands--configuration)

---

## 1. What is BPDU Guard?

- **BPDU Guard** is a Spanning Tree safety feature that **shuts down a port instantly** if it receives a **BPDU** (a Spanning Tree "hello" message).
- It is used on **edge ports** (PortFast ports) that should only connect to **end devices** — never to other switches.
- Its primary goal: to **protect the network from loops** caused by accidentally (or maliciously) plugging a switch into the wrong port.

---

## 2. Why do we need it? (The Problem it Solves)

- PortFast ports skip the normal loop checks for speed — but if someone plugs a **switch** into one, a dangerous **network loop** can form.
- A loop can flood the network with traffic and cause a **total meltdown** in seconds.
- BPDU Guard solves the **"someone plugged in the wrong device" problem** by killing the port the moment it sees switch traffic.

---

## 3. How it relates to the broader network

- BPDU Guard is part of **Spanning Tree Protocol (STP)** and is almost always paired with **PortFast**.
- It acts as the **safety net** that makes PortFast's speed safe to use.
- **Example:** Think of BPDU Guard like a **fire alarm with automatic door locks**. PortFast opens a fast door for trusted people, but the instant smoke (a BPDU from a rogue switch) is detected, the door **slams shut and locks** to protect the whole building.

---

## 4. Key Component: BPDU Detection

* **What it is:** The mechanism that listens for any **BPDU** arriving on a protected port.
* **Why it matters:** A BPDU means a **switch** is connected where only an end device should be — a clear violation.
* **Example:** Like a **metal detector** that beeps the moment something forbidden passes through.

---

## 5. Key Component: Err-Disable Action

* **What it is:** The response that puts the violating port into the **err-disabled (shut down)** state.
* **Why it matters:** It **immediately isolates** the threat before a loop can spread.
* **Example:** Like an **automatic circuit breaker** that trips to stop an electrical fire from spreading.

---

## 6. Key Component: PortFast Pairing

* **What it is:** BPDU Guard is designed to work **on PortFast (edge) ports**.
* **Why it matters:** Since PortFast skips loop checks, BPDU Guard restores that protection in a safe way.
* **Example:** Like a **seatbelt that pairs with a fast sports car** — the speed is only safe because the safety device is there too.

---

## 7. Safety & Security Features

* **What it is:** Instant port shutdown plus optional **Errdisable Recovery** (auto re-enable after a timer) and logging/alerts.
* **Why it matters:** It stops both **accidental loops** and **malicious rogue-switch attacks**, while keeping admins informed.
* **Example:** Like a **bank vault that locks down and sounds an alarm** the instant an unauthorized entry is detected.

---

## 8. Who created BPDU Guard

* **Cisco Systems**, as a Spanning Tree protection enhancement.

    **Quick facts:**
    * **Role:** Developer of the BPDU Guard STP safety feature.
    * **Invention/Creation:** BPDU Guard, to protect PortFast edge ports from loops.
    * **Purpose:** To automatically disable any edge port that receives a BPDU.
    * **Recognition/Standard:** A Cisco enhancement built on the IEEE **802.1D / 802.1w** STP framework.

---

## 9. Types / Variations of BPDU Guard

- **Interface-Level BPDU Guard**
- **Global (Default) BPDU Guard**

#### Interface-Level BPDU Guard
* **What it is:** BPDU Guard enabled manually on **one specific port**.
* **Why use it:** For precise control on individual critical ports.
* **Example:** Like installing a **single alarm on one important door**.

#### Global (Default) BPDU Guard
* **What it is:** Enabled switch-wide so **all PortFast ports** are automatically protected.
* **Why use it:** A best-practice safety net that covers every edge port at once.
* **Example:** Like wiring **alarms into every door of the building** at once.

---

## 10. Flow of Phases / How it Works

### Phase 1: Normal Operation
* **The Action:** A PortFast port with BPDU Guard forwards end-device traffic normally.
* **The Logic:** No BPDUs are expected from an end device.
* **The Result:** Traffic flows fast and freely.

### Phase 2: BPDU Arrives
* **The Action:** A BPDU is received on the protected port.
* **The Logic:** The switch knows only a **switch** sends BPDUs — this is a violation.
* **The Result:** A violation is flagged immediately.

### Phase 3: Port Shutdown
* **The Action:** The switch places the port into **err-disabled** state.
* **The Logic:** Disabling one port is far safer than risking a network-wide loop.
* **The Result:** The threat is **isolated instantly**; the rest of the network stays healthy.

### Phase 4: Recovery
* **The Action:** The port stays down until an admin re-enables it, or **Errdisable Recovery** does so automatically.
* **The Logic:** Recovery only happens once the cause is cleared or the timer expires.
* **The Result:** The port safely returns to service.

**Example in the Field:** Picture a **secure office with a fast-entry badge door**. Staff badge in instantly (normal operation). One day, someone tries to wedge a **forbidden device** in the doorway (BPDU arrives). The system **locks the door immediately** (shutdown), keeping the rest of the office safe. Security later inspects and **resets the lock** once it's clear (recovery).

---

## 11. States and Timers

- **Forwarding State:** Normal state while no BPDU is detected.
- **Err-Disabled State:** Where the port lands instantly after a BPDU violation.
- **Errdisable Recovery Timer:** Default **300 seconds (5 minutes)** — optional auto-recovery interval.
- **No Delay on Trigger:** Shutdown happens **immediately** upon BPDU detection (no waiting timer).

---

## 12. Advanced / Extra Features

- **Errdisable Recovery:** Automatically re-enables ports after a set time, reducing manual work.
- **Global vs Per-Port Control:** Apply protection switch-wide or selectively.
- **Logging & SNMP Traps:** Alerts admins the moment a violation occurs.
- **Pairing with BPDU Filter:** A related (but different) feature that *ignores* BPDUs — use carefully, as it does not shut the port down.
- **Loop Guard & Root Guard:** Complementary STP protections for non-edge (switch-to-switch) links.

---

## 13. Related Commands & Configuration

* **Command:** `spanning-tree bpduguard enable`
* **What it does:** Enables BPDU Guard on the selected interface.
* **When to use it:** **Setup/security** — on individual PortFast edge ports.

---

* **Command:** `spanning-tree portfast bpduguard default`
* **What it does:** Globally enables BPDU Guard on **all PortFast-enabled ports**.
* **When to use it:** **Setup/security** — the recommended switch-wide best practice.

---

* **Command:** `spanning-tree bpduguard disable`
* **What it does:** Disables BPDU Guard on a specific interface (overrides the global default).
* **When to use it:** **Setup** — for a port that legitimately needs an exception.

---

* **Command:** `errdisable recovery cause bpduguard`
* **What it does:** Allows ports shut down by BPDU Guard to recover automatically.
* **When to use it:** **Setup/troubleshooting** — to avoid manual re-enabling.

---

* **Command:** `errdisable recovery interval [seconds]`
* **What it does:** Sets how long (default 300s) before an err-disabled port auto-recovers.
* **When to use it:** **Setup/tuning** — to control recovery timing.

---

* **Command:** `shutdown` then `no shutdown`
* **What it does:** Manually resets an err-disabled port back to active.
* **When to use it:** **Troubleshooting** — after fixing the cause of the violation.

---

* **Command:** `show spanning-tree summary`
* **What it does:** Displays global STP settings, including BPDU Guard default status.
* **When to use it:** **Verifying** — to confirm switch-wide protection is on.

---

* **Command:** `show spanning-tree interface [port] detail`
* **What it does:** Shows detailed STP info for a port, including BPDU Guard status.
* **When to use it:** **Verifying** — to check protection on a specific port.

---

* **Command:** `show interfaces status err-disabled`
* **What it does:** Lists all ports currently in the err-disabled state.
* **When to use it:** **Troubleshooting** — to find ports shut down by BPDU Guard.

---

* **Command:** `show errdisable recovery`
* **What it does:** Displays which err-disable causes have auto-recovery enabled and the timer.
* **When to use it:** **Verifying** — to confirm recovery settings.

---
