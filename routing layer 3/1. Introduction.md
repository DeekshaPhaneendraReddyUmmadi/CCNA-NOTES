# A Comprehensive Guide to Network Routing

## 1. What is Routing?
Routing is the process of selecting a path for traffic in a network or between or across multiple networks. In the context of computer networking, it occurs at **Layer 3 (the Network Layer)** of the OSI (Open Systems Interconnection) model. 

The primary device responsible for this is the **router**. A router acts as a dispatcher, examining the destination IP address of an incoming data packet, consulting its internal map (the routing table), and forwarding the packet to the best next step (next hop) toward its final destination.

## 2. Routed and Routing Protocols

A common point of confusion in networking is the difference between these two similarly named concepts. They serve entirely different functions:

* **Routed Protocols:** These are the protocols used to transport user data across a network. A routed protocol provides enough information in its network layer address (like an IP address) to allow a router to forward it to the next node. 
  * *Function:* Carries the actual payload/data (emails, web pages, file transfers).
  * *Modern Examples:* **IPv4** and **IPv6**.
  * *Legacy Examples:* IPX/SPX, AppleTalk.
* **Routing Protocols:** These are the protocols used *by routers* to communicate with other routers. They share network topology information to build and maintain the routing table. 
  * *Function:* Does not carry user data; instead, it determines the path that the user data (the routed protocol) will take.
  * *Examples:* **OSPF, BGP, EIGRP, RIP**.

## 3. The History and Evolution of Routing
The history of routing is intrinsically tied to the creation of the Internet.

* **1960s - The ARPANET Era:** The concept of packet switching was developed. The first routers were called Interface Message Processors (IMPs). They used a very basic Distance Vector algorithm based on the Bellman-Ford equation to pass data between early research computers.
* **1980s - The Birth of Modern Protocols:** As networks grew, the original algorithms could not scale. The **Routing Information Protocol (RIP)** was standardized in 1988, using "hop count" to determine paths. Around the same time, the concept of Autonomous Systems (AS) was created to divide the growing internet into manageable domains, leading to the first Exterior Gateway Protocol (EGP).
* **1990s - The Need for Scale:** The commercialization of the Internet caused an explosion in routing tables. Classful IP addressing was abandoned for Classless Inter-Domain Routing (CIDR) to save IP addresses. **OSPF (Open Shortest Path First)** was introduced as a highly scalable link-state protocol. Simultaneously, **BGP (Border Gateway Protocol)** replaced EGP to handle the massive, complex routing policies required by global Internet Service Providers (ISPs).
* **2000s to Present - Advanced and Software-Defined Routing:** Cisco developed **EIGRP**, an advanced distance-vector protocol. Today, routing is evolving into **Software-Defined Networking (SDN)** and **SD-WAN**, where the "control plane" (the brain making routing decisions) is separated from the "data plane" (the hardware forwarding the packets), allowing for centralized, AI-driven network management.

## 4. Core Routing Concepts and Terminology
To understand how routing works, you must understand the metrics and tools routers use to make decisions:

* **Routing Table:** A database stored in the router's RAM. It contains network destinations, the interface to use to reach them, and the "cost" to get there.
* **Longest Prefix Match:** When a router looks at a destination IP, it checks its routing table for the most specific match (the longest subnet mask) and uses that path.
* **Metric:** A calculated value used to determine the "best" path when multiple paths to the same destination exist. Metrics can be based on hop count, bandwidth, delay, reliability, or load.
* **Administrative Distance (AD):** A measure of trustworthiness. If a router learns about the exact same destination from two different protocols (e.g., OSPF and RIP), it uses AD to decide which protocol to trust. A lower AD is more trusted.
* **Autonomous System (AS):** A large network or group of networks that has a unified routing policy. Every ISP, large enterprise, or university is an AS and is assigned an AS Number (ASN).

## 5. Types of Routing
There are three primary ways a router populates its routing table:

* **Static Routing:** A network administrator manually types the routes into the router. 
  * *Pros:* Highly secure, uses zero network bandwidth, predictable.
  * *Cons:* Does not scale. If a link goes down, the router cannot automatically adapt, and traffic is dropped.
* **Dynamic Routing:** Routers run software protocols to communicate with each other, automatically sharing information about the networks they know and adapting to topology changes.
  * *Pros:* Highly scalable, automatically routes around failures.
  * *Cons:* Consumes CPU, memory, and network bandwidth; requires complex configuration.
* **Default Routing:** A special type of static route (often called the "Gateway of Last Resort"). It tells the router: "If you do not have a specific route for a destination in your routing table, send the packet here." This is how your home router sends traffic to the Internet.

## 6. Dynamic Routing Algorithms
Dynamic routing protocols operate using specific mathematical algorithms. There are three main types:

* **Distance Vector:** Routers share their entire routing table, but only with their directly connected neighbors. It is often called "routing by rumor" because a router relies completely on its neighbor's perspective of the network. *(Algorithm: Bellman-Ford)*.
* **Link-State:** Every router maps the exact topology of the entire network. Routers only share information about the state of their own direct links, but they flood this information to *everyone* in the network. Each router then independently calculates the shortest path to every destination. *(Algorithm: Dijkstra's Shortest Path First)*.
* **Path Vector:** Similar to distance vector, but instead of looking at routers, it looks at entire Autonomous Systems. It keeps track of the exact path of ASNs a packet must traverse, which prevents routing loops on a global scale.

## 7. Major Routing Protocols (Subtopics)
Routing protocols are divided into two categories: Interior Gateway Protocols (used within a single AS) and Exterior Gateway Protocols (used to connect different ASes).

### Interior Gateway Protocols (IGP):
* **RIP (Routing Information Protocol):** A legacy Distance Vector protocol. It uses hop count as its metric. The maximum allowed hops is 15; a network 16 hops away is considered unreachable. Rarely used today except in very small, legacy networks.
* **OSPF (Open Shortest Path First):** The most widely used enterprise Link-State protocol. It uses "Cost" (based on link bandwidth) as its metric. It divides networks into "Areas" to keep routing tables small and efficient.
* **IS-IS (Intermediate System to Intermediate System):** Another Link-State protocol, very similar to OSPF. It is highly scalable and is the preferred IGP for large telecommunications providers and ISP backbones.
* **EIGRP (Enhanced Interior Gateway Routing Protocol):** Originally a Cisco proprietary protocol, now an open standard. It is an "Advanced Distance Vector" protocol that uses a composite metric of bandwidth and delay. It is incredibly fast at converging (adapting to changes) using the DUAL (Diffusing Update Algorithm).

### Exterior Gateway Protocols (EGP):
* **BGP (Border Gateway Protocol):** The only EGP in use today. BGP is the protocol that runs the Internet. It is a Path Vector protocol. Instead of finding the "fastest" path, BGP finds the best path based on complex administrative policies, business agreements between ISPs, and AS paths.

## 8. The Step-by-Step Routing Process
To tie this all together, here is exactly what happens when a packet is routed:

1. **Generation:** A user's computer generates a packet destined for a web server. The computer notices the server is on a different network, so it sends the packet to its Default Gateway (the local router).
2. **De-encapsulation:** The router receives the frame, checks the Layer 2 MAC address, strips off the Layer 2 header, and examines the Layer 3 IP packet.
3. **Table Lookup:** The router looks at the Destination IP address and consults its routing table. 
4. **Decision:** The router finds the longest prefix match for that IP address. The table dictates which outgoing interface to use and the IP address of the "next hop" router.
5. **Re-encapsulation:** The router packages the IP packet into a new Layer 2 frame addressed to the MAC address of the next hop router.
6. **Forwarding:** The router sends the packet out of the interface. This process repeats at every router until the packet reaches the destination network.