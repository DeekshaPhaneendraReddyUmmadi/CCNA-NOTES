# A Deep Dive into Static Routing

While dynamic routing protocols (like OSPF or BGP) often get the spotlight for their ability to automatically adapt to network changes, static routing remains a fundamental and highly critical component of network design. 

Here is a comprehensive overview of static routing, how it works, its variations, and when to use it.

---

## 1. What is Static Routing?
Static routing is the process where a network administrator manually configures and adds routing entries into a router's routing table. Unlike dynamic routing, where routers communicate with each other to learn about the network topology automatically, a static route tells the router exactly where to send traffic for a specific destination, and this path will never change unless the administrator manually updates or deletes it.

By default, on Cisco routers, a static route has an **Administrative Distance (AD) of 1**. This means it is considered highly trustworthy—more trustworthy than any dynamic routing protocol (for example, OSPF has an AD of 110).

## 2. Types of Static Routes
Network engineers use different types of static routes to accomplish specific goals:

* **Standard Static Route:** This is a manually configured route to a specific remote network. You tell the router the destination network IP, the subnet mask, and the "next-hop" IP address (or the exit interface) to reach it.
* **Default Static Route:** Often called the "Gateway of Last Resort." This is a route that matches all packets. It is written as `0.0.0.0 0.0.0.0`. It tells the router: *"If you look in your routing table and cannot find a specific match for a destination IP, send the packet here."* This is how your home router sends traffic out to your ISP.
* **Summary Static Route:** To reduce the size of the routing table, an administrator can combine multiple specific static routes into a single, broader static route (route summarization). 
* **Floating Static Route:** This is a brilliant use of static routing for backup purposes. An administrator creates a static route but manually assigns it an Administrative Distance *higher* than the primary dynamic routing protocol. For example, if OSPF (AD 110) is the primary path, you might create a floating static route with an AD of 115. The router will hide this static route until the OSPF route fails; if OSPF goes down, the floating static route "floats" into the routing table to act as a backup.

## 3. Advantages of Static Routing
* **Minimal Resource Usage:** Because the router doesn't have to run complex algorithms or process routing updates from neighbors, static routing uses virtually zero CPU and memory.
* **Zero Bandwidth Overhead:** Dynamic protocols constantly send update packets across the network links. Static routes do not send any updates, saving bandwidth.
* **Security:** Since routers are not advertising their networks or accepting routing updates, it is much harder for a malicious actor to inject fake routes or map out your network topology.
* **Predictability:** Traffic will always take the exact path the administrator specified. There are no unexpected routing changes.

## 4. Disadvantages of Static Routing
* **Lack of Fault Tolerance:** This is the biggest drawback. If a cable gets cut or a next-hop router goes offline, the static route does not automatically route around the failure. The router will continue trying to send traffic into a "black hole" until an administrator manually fixes it.
* **Administrative Burden:** In a large network with hundreds of routers, manually typing in every route on every router is impossible and highly prone to human error.
* **Does Not Scale:** As a network grows, maintaining static routes becomes an unmanageable task.

## 5. When Should You Use Static Routing?
Even in massive enterprise networks running advanced dynamic protocols, static routing is still used strategically. The most common use cases include:

1. **Stub Networks:** A stub network is a network accessed by a single path (only one way in, one way out). There is no need for a dynamic protocol here; a single static route pointing out of the stub, and a single static route pointing into the stub, is the most efficient design.
2. **Connecting to an ISP:** Most businesses use a default static route pointing to their Internet Service Provider, as they do not need to learn the entire internet's routing table (which contains nearly a million routes).
3. **Creating Backups:** Using floating static routes to provide a backup path over a secondary link (like a cellular backup connection) if the primary fiber link fails.
4. **High-Security Networks:** If you have a highly sensitive VLAN or network segment, you might use static routing to ensure that only specific, manually verified paths can reach it, preventing dynamic protocols from accidentally advertising it to the rest of the company.