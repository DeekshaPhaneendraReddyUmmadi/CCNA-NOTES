### Spanning Tree Protocol
---

### 1. What is STP? (Spanning Tree Protocol)

STP is a safety protocol used by switches. It does not help the switch learn MAC addresses. Instead, its only job is to act like a traffic cop to prevent data from running in endless circles.


### 2. Why do we need it? (Stopping Loops)

In a good network, you usually connect switches with extra backup cables just in case one cable breaks. But this creates a physical circle (a loop).

If a computer sends a broadcast message (like ARP), the switches will forward it around and around that circle forever. This is called a "broadcast storm," and it will quickly crash the entire network. STP stops this by finding the circle and temporarily turning off (blocking) one of the backup ports.


### 3. How it relates to the Switching Table

If data loops in a circle, a switch will see the same computer's MAC address coming from Port 1, then Port 2, then Port 1 again. The switching table gets confused and constantly rewrites itself (called MAC flapping). STP keeps the network paths straight so the switching table stays accurate.


**Example:**
Imagine a town where the roads form a perfect circle. If a lost driver keeps driving around looking for a house, they will circle forever and cause a traffic jam. STP is like a police officer who puts a "Road Closed" barricade on one part of the circle. The traffic jam is fixed. If a different road in town washes away in a storm, the officer removes the barricade so people can use the backup road.

### Who created STP

* Radia Perlman

    **Quick facts:**

    * **Role:** American computer programmer and network engineer. 
Invention: Designed STP in 1985 while working for Digital Equipment Corporation (DEC). 
    * **Purpose:** The protocol prevents loops in Ethernet networks, enabling the construction of large, reliable networks. 
    * **Recognition:** Inducted into the Internet Hall of Fame in 2014; holds over 100 patents. 
    * **Nickname:** Often called the "Mother of the Internet," a title she has publicly declined. 
    * Perlman’s invention was standardized by the IEEE as 802.1D in 1990 and remains a fundamental component of modern network infrastructure. 

### Types:

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

