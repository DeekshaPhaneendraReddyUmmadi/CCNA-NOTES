# 📄 `PortFast`

## 📑 Index

1. [What is PortFast?](#1-what-is-portfast)
2. [Why do we need it? (The Problem it Solves)](#2-why-do-we-need-it-the-problem-it-solves)
3. [How it relates to the broader network](#3-how-it-relates-to-the-broader-network)
4. [Key Component 1 — Instant Forwarding](#4-key-component-1--instant-forwarding)
5. [Key Component 2 — TCN Suppression](#5-key-component-2--tcn-suppression)
6. [Key Component 3 — RSTP Edge Port Equivalency](#6-key-component-3--rstp-edge-port-equivalency)
7. [Safety & Security Features](#7-safety--security-features)
8. [Who created it / Standards](#8-who-created-it--standards)
9. [Types / Variations](#9-types--variations)
10. [Flow of Phases / How it Works](#10-flow-of-phases--how-it-works)
11. [States and Timers](#11-states-and-timers)
12. [Advanced / Extra Features](#12-advanced--extra-features)
13. [Configuration & Troubleshooting Workflow](#13-configuration--troubleshooting-workflow)

---

## 1. What is PortFast?

- **PortFast** is a Cisco spanning-tree feature that forces a port to bypass the normal STP listening and learning phases, transitioning **immediately to the forwarding state** the moment the link comes up.
- It is designed **strictly for edge ports** connected to single end-hosts (PCs, servers, IP phones).
- **Analogy** 🎟️: A **VIP Fast-Pass lane** at a concert. Instead of standing in the 30-minute security line (Listening/Learning) to prove you aren't a threat (a loop), you are pre-trusted and walk straight onto the floor (Forwarding) the second you arrive.

## 2. Why do we need it? (The Problem it Solves)

- Classic STP takes **30 to 50 seconds** to transition a port to forwarding.
- Modern PCs boot up and request a DHCP address in **under 10 seconds**.
- **The Problem:** By the time the PC asks for an IP, the switch port is still in the "Listening" state and drops the DHCP request. The PC gives up and assigns itself an APIPA/169.254.x.x address.
- PortFast solves this by providing **instant connectivity** for hosts, ensuring DHCP, PXE boot, and domain logins succeed on the first try.

## 3. How it relates to the broader network

- Applied to **all host-facing access ports** on your access switches (`ACC-SW1–4`).
- Crucial for your **PCs in VLAN 20/30** and **IP Phones in VLAN 40**.
- **Never** applied to the trunk uplinks between `ACC-SW1` and `CORE-SW1/2` (doing so would create a massive loop risk).

## 4. Key Component 1 — Instant Forwarding

- When the physical link goes `up`, PortFast skips the 15-second **Listening** state and the 15-second **Learning** state.
- The port goes straight to **Forwarding**.
- **Note:** STP is *not* disabled. The port still sends BPDUs and listens for them. If it receives a BPDU, it realizes it's connected to a switch and **loses its PortFast status**, reverting to normal STP rules.

## 5. Key Component 2 — TCN Suppression

- Normally, any port going up or down generates a **Topology Change Notification (TCN)**, which forces all switches in the network to rapidly flush their MAC/CAM tables.
- If 500 employees turn off their PCs at 5:00 PM, the network would suffer 500 TCNs and massive MAC table thrashing.
- **PortFast suppresses TCNs.** When a PortFast port bounces, the switch quietly ignores it and does *not* send a TCN to the Root Bridge.

## 6. Key Component 3 — RSTP Edge Port Equivalency

- In Rapid-PVST+ (802.1w), the concept of PortFast was officially standardized as the **"Edge Port"** role.
- Configuring `spanning-tree portfast` on a Rapid-PVST+ switch simply tells the switch: *"This is an 802.1w Edge Port."*

## 7. Safety & Security Features

- 🚨 **The Danger:** If someone plugs a switch into a PortFast port and creates a physical loop, the port will forward traffic instantly. A broadcast storm will rage for a few seconds until the switch finally processes a BPDU and blocks the port.
- **The Solution:** PortFast must **always** be paired with **BPDU Guard**. BPDU Guard ensures that if a switch is plugged in, the port is instantly err-disabled before a loop can form.

## 8. Who created it / Standards

- Originally a **Cisco-proprietary** enhancement to 802.1D.
- Later adopted by the IEEE in the 802.1w (RSTP) standard under the name **Edge Port**.

## 9. Types / Variations

| Mode | Command | Use Case |
|------|---------|----------|
| **Global Default** | `spanning-tree portfast default` | Auto-enables on all non-trunking (access) ports. |
| **Interface Access** | `spanning-tree portfast` | Enables on a specific access port. |
| **Interface Trunk** | `spanning-tree portfast trunk` | Enables on a *trunk* port connected to a single host (e.g., an ESXi server or Router-on-a-Stick). |

## 10. Flow of Phases / How it Works

```mermaid
flowchart TD
    A[PC1 powered on] --> B[ACC-SW1 Fa0/1 Link UP]
    B --> C{PortFast Enabled?}
    C -- No --> D[Listening 15s]
    D --> E[Learning 15s]
    E --> F[Forwarding (DHCP likely timed out)]
    C -- Yes --> G[Bypass LIS/LRN]
    G --> H[FORWARDING instantly]
    H --> I[PC1 sends DHCP Discover successfully]
    H --> J[No TCN generated for the network]
```

## 11. States and Timers

| STP Phase | Standard Delay | PortFast Delay |
|-----------|----------------|----------------|
| **Listening** | 15 seconds | **0 seconds (Skipped)** |
| **Learning** | 15 seconds | **0 seconds (Skipped)** |
| **Total Time to Forward** | **30 seconds** | **< 1 second** |

## 12. Advanced / Extra Features

- **`spanning-tree portfast trunk`**: A special command used when a server (like VMware ESXi) requires multiple VLANs (a trunk) but is still an end-host that won't create a loop. It applies PortFast rules to a trunk link.
- **Macro command**: In newer IOS versions, `switchport host` is a macro that automatically configures `switchport mode access` AND `spanning-tree portfast` in one line.

---

## 13. Configuration & Troubleshooting Workflow

### Phase 1: Port Selection & Preparation
- Target **only** the ports connected to PCs and IP Phones on your access switches.
- **Never** select the `GigabitEthernet` uplinks going to `CORE-SW1/2`.
```
ACC-SW1> enable
ACC-SW1# configure terminal
ACC-SW1(config)# interface range FastEthernet0/1 - 8
ACC-SW1(config-if-range)# description ** PC and Phone Edge Ports **
ACC-SW1(config-if-range)# switchport mode access
```

### Phase 2: Base Configuration
- Enable PortFast globally (recommended) or per-interface.
```
! --- Option A: Global (Applies to all access ports automatically) ---
ACC-SW1(config)# spanning-tree portfast default

! --- Option B: Per-Interface ---
ACC-SW1(config)# interface range FastEthernet0/1 - 8
ACC-SW1(config-if-range)# spanning-tree portfast
```
*Note: When you type this, IOS will give you a loud warning: "%Warning: portfast should only be enabled on ports connected to a single host..."*

### Phase 3: Hardening & Security
- **Mandatory:** Always pair PortFast with BPDU Guard to protect against rogue switches.
```
ACC-SW1(config)# spanning-tree portfast bpduguard default
! (Or per-interface: spanning-tree bpduguard enable)
```
- **Why:** PortFast removes the safety delay. BPDU Guard acts as the safety net, instantly killing the port if a BPDU (switch) is detected.

### Phase 4: Verification Flow
Run these `show` commands **in this order**:
```
ACC-SW1# show spanning-tree summary
ACC-SW1# show spanning-tree interface FastEthernet0/1 portfast
ACC-SW1# show spanning-tree interface FastEthernet0/1 detail
```
- **What to look for:**
  - `show spanning-tree summary` → Look for **"PortFast Default is enabled"**.
  - `show ... portfast` → Should return **"VLAN20 is enabled"**.
  - `show ... detail` → Look for **"The port is in the portfast mode"** and confirm it is in the **FWD** (Forwarding) state immediately after plugging in a device.

### Phase 5: Advanced Debugging
- If PCs are failing to get IP addresses, or the network is experiencing high CPU:
```
ACC-SW1# debug spanning-tree events
ACC-SW1# show spanning-tree detail | include changes
ACC-SW1# show interfaces status err-disabled
```
- **Troubleshooting logic:**
  - **PCs getting 169.254.x.x IPs** → PortFast is likely missing. The PC gives up on DHCP before the 30-second STP delay finishes. Enable PortFast.
  - **Constant Topology Changes (TCs)** → Check `show spanning-tree detail`. If the TC counter is rising rapidly, an edge port is missing PortFast and is generating a TCN every time a PC goes to sleep/wakes up.
  - **Port goes err-disabled** → BPDU Guard caught a switch plugged into a PortFast port. Find the rogue switch and remove it.
  - **Accidental loop on a trunk** → Someone typed `spanning-tree portfast trunk` on a switch-to-switch link. Remove it immediately.
