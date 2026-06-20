# Spanning Tree Protocol

### Index
* [1. What is STP? (Spanning Tree Protocol)](#1-what-is-stp-spanning-tree-protocol)
* [2. Why do we need it? (Stopping Loops)](#2-why-do-we-need-it-stopping-loops)
* [3. How it relates to the Switching Table](#3-how-it-relates-to-the-switching-table)
* [4. The Boss (Root Bridge)](#4-the-boss-root-bridge)
* [5. The Secret Messages (BPDUs)](#5-the-secret-messages-bpdus)
* [6. Port Roles (Open or Closed Doors)](#6-port-roles-open-or-closed-doors)
* [7. Safety Features (PortFast & BPDU Guard)](#7-safety-features-portfast--bpdu-guard)
* [8. Who created STP](#who-created-stp)
* [Types:](#types)
  * [1. IEEE 802.1D](#1-ieee-8021d)
  * [2. RSTP (Rapid STP)](#2-rstp-rapid-stp)
  * [3. PVST+ (Per-VLAN STP)](#3-pvst-per-vlan-stp)
  * [4. MSTP (Multiple STP)](#4-mstp-multiple-stp)
  * [5. Common Spanning Tree](#5-common-spanning-tree)
* [9. Flow of STP Phases](#9-flow-of-stp-phases)
    * [1. The Root Election (BPDU Exchange)](#1-the-root-election-bpdu-exchange)
    * [2. Path Cost & Root Port (RP) Selection](#2-path-cost--root-port-rp-selection)
    * [3. Segment Resolution & Designated Ports (DP)](#3-segment-resolution--designated-ports-dp)
    * [4. Loop Prevention (Blocking State)](#4-loop-prevention-blocking-state)
    * [5. Steady State & Topology Changes (TCN)](#5-steady-state--topology-changes-tcn)
* [10. The 5 Port States (The Transition)](#10-the-5-port-states-the-transition)  
* [11. The 3 Main Timers (The Clocks)](#11-the-3-main-timers-the-clocks)  
* [12. Extended System ID (VLAN Tagging)](#12-extended-system-id-vlan-tagging)  
* [13. Extra Safety Features (Root Guard & Loop Guard)](#13-extra-safety-features-root-guard--loop-guard) 
--- 

## 1. What is STP? (Spanning Tree Protocol)

Spanning Tree Protocol (STP) is a Layer 2 network protocol defined by IEEE 802.1D that prevents network loops and broadcast storms in Ethernet networks by creating a loop-free logical topology.  It achieves this by electing a root bridge and using Bridge Protocol Data Units (BPDUs) to calculate the shortest path to the root, while logically blocking redundant paths to ensure only one active path exists between any two network nodes. 

## 2. Why do we need it? (Stopping Loops)

In a good network, you usually connect switches with extra backup cables just in case one cable breaks. But this creates a physical circle (a loop).

If a computer sends a broadcast message (like ARP), the switches will forward it around and around that circle forever. This is called a "broadcast storm," and it will quickly crash the entire network. STP stops this by finding the circle and temporarily turning off (blocking) one of the backup ports.

## 3. How it relates to the Switching Table

If data loops in a circle, a switch will see the same computer's MAC address coming from Port 1, then Port 2, then Port 1 again. The switching table gets confused and constantly rewrites itself (called MAC flapping). STP keeps the network paths straight so the switching table stays accurate.

**Example:**
Imagine a town where the roads form a perfect circle. If a lost driver keeps driving around looking for a house, they will circle forever and cause a traffic jam. STP is like a police officer who puts a "Road Closed" barricade on one part of the circle. The traffic jam is fixed. If a different road in town washes away in a storm, the officer removes the barricade so people can use the backup road.

## 4. The Boss (Root Bridge)

* **What it is:** STP forces all the switches to hold an election and pick one switch to be the absolute center of the network. This leader is called the Root Bridge.
* **Why it matters:** Once the boss is chosen, every other switch calculates the absolute fastest, shortest cable path to reach that boss.
* **Example:** Think of the Root Bridge as the center of a city. All the traffic lights and one-way streets are designed to help cars get to the city center as fast as possible.

## 5. The Secret Messages (BPDUs)

* **What it is:** BPDU stands for Bridge Protocol Data Unit. These are the actual, invisible "hello" messages that switches send to each other every 2 seconds to map out the network.
* **Why it matters:** This is how switches know if a cable breaks. If a switch suddenly stops hearing BPDUs on a port, it knows the path is dead and it needs to open a backup road.
* **Example:** Like security guards doing a radio check every 2 seconds. If a guard goes silent, the others instantly know there is a problem.

## 6. Port Roles (Open or Closed Doors)

* **What it is:** Based on where the Root Bridge is, STP gives a specific "job" to every single plug (port) on the switch.
The Jobs:
* **Root Port:**  The single fastest door pointing toward the boss. (Always open).
* **Designated Port:** A normal door pointing away from the boss. (Always open).
* **Blocking Port:** The backup door. (Closed to stop the loop).
* **Example:** Like traffic lights. Green means go (Root/Designated), and Red means stop (Blocking).

## 7. Safety Features (PortFast & BPDU Guard)

* **What it is:** Special settings engineers use to make STP work better with normal devices like laptops and printers.
* **Why it matters:** Normal computers do not cause network loops, so they shouldn't have to wait 50 seconds for STP to check the cable. PortFast lets a computer connect to the internet instantly. BPDU Guard is a security feature that instantly shuts down the port if someone tries to plug an unauthorized switch into a PortFast plug.
* **Example:** PortFast is like a VIP fast-pass lane for regular employees. BPDU Guard is the security bouncer that instantly kicks out anyone trying to sneak unauthorized equipment through the VIP lane.

## 8. Who created STP

* Radia Perlman

    **Quick facts:**

    * **Role:** American computer programmer and network engineer. 
Invention: Designed STP in 1985 while working for Digital Equipment Corporation (DEC). 
    * **Purpose:** The protocol prevents loops in Ethernet networks, enabling the construction of large, reliable networks. 
    * **Recognition:** Inducted into the Internet Hall of Fame in 2014; holds over 100 patents. 
    * **Nickname:** Often called the "Mother of the Internet," a title she has publicly declined. 
    * Perlman’s invention was standardized by the IEEE as 802.1D in 1990 and remains a fundamental component of modern network infrastructure. 

## Types:

* 802.1D(depricated)
* RSTP (Rapid STP)
* PVST+ (Per-VLAN STP)
* Multiple Spanning Tree

### 1. IEEE 802.1D
* **What it is:** The very first version of Spanning Tree Protocol.
* **Why use it:** It does a great job of stopping loops, but it is very slow. If a main cable breaks, it can take up to 50 seconds for this STP to open the backup cable.
* **Example:** Like an old computer that takes a full minute to turn on and load your programs.
* The leagacy standard of spanning tree.
* When bridges were around

### 2. RSTP (Rapid STP)
*   **What it is:** The upgraded, modern version of classic STP. 
*   **Why use it:** As the name says, it is rapid (fast). If a cable breaks, it can switch to the backup path in just 1 or 2 seconds. This is the standard used on most basic networks today.
*   **Example:** Like a modern smartphone that turns on and opens apps almost instantly.

### 3. PVST+ (Per-VLAN STP)
*   **What it is:** A special version created by Cisco. It runs a completely separate STP process for every virtual network (VLAN) you have.
*   **Why use it:** It lets you use all your cables at the same time. You can send Network A down the left cable, and Network B down the right cable. 
*   **Example:** Imagine a school where instead of one crossing guard for everyone, you hire a dedicated crossing guard for every single classroom. 

### 4. MSTP (Multiple STP)
*   **What it is:** The smart, highly efficient version used in huge networks. 
*   **Why use it:** Running a separate STP for 500 different virtual networks (like PVST+ does) makes the switch work too hard. MSTP lets you group those networks together to save memory and power.
*   **Example:** Instead of a crossing guard for all 500 classrooms, you group them up: one guard for the elementary kids, one for the middle schoolers, and one for the high schoolers.  
 
### 5. Common Spanning Tree
* Assumes that there is one spanning tree instance for the entire bridge network.  
 

## 9. Flow of STP Phases

### 1. The Root Election (BPDU Exchange)
*   **The Action:** When switches boot up, they immediately start sending out Configuration BPDUs (Bridge Protocol Data Units) every 2 seconds. 
*   **The Logic:** Initially, every switch claims to be the Root Bridge. They compare their **Bridge ID (BID)**, which is made of a Priority value (default 32768) plus the switch's base MAC address.
*   **The Result:** The switch with the lowest BID wins the election and becomes the Root Bridge. 

### 2. Path Cost & Root Port (RP) Selection
*   **The Action:** All non-root switches must find the most efficient path to the Root Bridge.
*   **The Logic:** They calculate the **Root Path Cost** based on the bandwidth of the links. For example, a 1 Gbps link has a cost of 4, and a 100 Mbps link has a cost of 19. 
*   **The Result:** The single port on each switch with the lowest cumulative cost to the Root Bridge becomes the **Root Port (RP)**. It transitions to the Forwarding state.

### 3. Segment Resolution & Designated Ports (DP)
*   **The Action:** Now, the switches must decide which side of every individual cable (segment) is allowed to send traffic.
*   **The Logic:** The switches on opposite ends of a cable compare their total cost to the Root Bridge. The switch with the lower cost wins control of that segment.
*   **The Result:** The winning port becomes the **Designated Port (DP)** and transitions to the Forwarding state. *(Note: All ports on the Root Bridge are automatically DPs).*

### 4. Loop Prevention (Blocking State)
*   **The Action:** The switches evaluate any ports that did not win a role.
*   **The Logic:** If a port is neither a Root Port nor a Designated Port, it means it is a redundant link creating a loop.
*   **The Result:** These leftover ports are placed in the **Blocking State**. They still listen to BPDUs to monitor network health, but they drop all standard data frames (like ARP or user traffic). The Layer 2 loop is now logically broken.

### 5. Steady State & Topology Changes (TCN)
*   **Running:** The network is stable. The Root Bridge sends a BPDU every 2 seconds. Downstream switches receive it on their Root Port, update it, and forward it out their Designated Ports.
*   **Adapting:** If a link goes down, or a switch stops hearing BPDUs for 20 seconds (Max Age timer), it detects a failure. 
*   **Fixing:** The switch sends a **Topology Change Notification (TCN)** BPDU. This tells all switches to immediately flush their MAC address tables (switching tables) because the physical paths have changed. A blocked port will then transition through Listening (15s) and Learning (15s) states before finally Forwarding traffic to restore the network.

**Example in the Field:**
If you have three switches wired in a triangle, Switch A (lowest MAC) becomes the Root. Switch B and C calculate their 1 Gbps direct links to A as their Root Ports. The link between B and C is redundant. B and C compare their costs; if they tie, they compare BIDs. If B has a lower BID, B's port becomes the Designated Port, and C's port goes into Blocking. The triangle is logically reduced to a "V" shape, stopping the loop.  


## 10. The 5 Port States (The Transition)

- **Disabled:** Manually shut down by an administrator.  
- **Blocking:** Listens for BPDUs, drops all other traffic to prevent loops.  
- **Listening (15 seconds):** Prepares to forward, sends and receives BPDUs, but drops data frames.  
- **Learning (15 seconds):** Learns MAC addresses but still does not forward data frames.  
- **Forwarding:** Fully operational, forwarding data and learning MAC addresses.  

## 11. The 3 Main Timers (The Clocks)

- **Hello Timer (2 seconds):** Interval between BPDU transmissions by the Root Bridge.  
- **Forward Delay (15 seconds):** Time spent in Listening and Learning states (total 30 seconds).  
- **Max Age Timer (20 seconds):** Time a switch waits without receiving BPDUs before declaring a link down and recalculating topology.

## 12. Extended System ID (VLAN Tagging)

Modern switches add the VLAN ID to the base priority in the Bridge ID to support per-VLAN STP instances. For example, VLAN 10 adds 10 to the base priority 32768, resulting in 32778.

## 13. Extra Safety Features (Root Guard & Loop Guard)

- **Root Guard:** Prevents a port from accepting BPDUs that would cause the switch to lose Root Bridge status.  
- **Loop Guard:** Prevents a blocking port from transitioning to forwarding if BPDUs stop due to unidirectional link failure.
