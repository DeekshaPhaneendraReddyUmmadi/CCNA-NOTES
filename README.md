# `README.md` (Root Directory)

> **Scope:** The ultimate, expert-level master reference guide for building, securing, and troubleshooting a Cisco Enterprise Campus Network.
> **Design:** Collapsed-Core topology featuring `CORE-SW1/2`, `ACC-SW1-4`, dynamic routing, network services, and comprehensive defense-in-depth security.

---

## 📂 Directory Structure & Clickable Index

This Knowledge Base is divided into five main pillars: Reference Guides, Layer 2 Switching, Layer 3 Routing, Network Services, and Security.

### 🔹 [Reference Guides](./reference-guides/)
| File | Topic |
|------|-------|
| [Protocols Overview](./reference-guides/protocols.md) | Master list of L2/L3 protocols and their functions. |
| [Common Show Commands](./reference-guides/common-show-commands.md) | The ultimate CLI cheat sheet for troubleshooting. |

### 🔹 [Layer 2 Switching](./layer-2-switching/)
*The foundation of the local area network, MAC addressing, and loop prevention.*

| File | Topic |
|------|-------|
| [Ethernet Frame](./layer-2-switching/ethernet-frame.md) | The L2 PDU, MAC addresses, and FCS. |
| [MAC / CAM Table](./layer-2-switching/mac-cam-table.md) | How switches learn and forward frames. |
| [Layer 2 Responsibilities](./layer-2-switching/layer-2-responsibilities.md) | LLC/MAC sublayers and media access. |
| [Network Hierarchy](./layer-2-switching/network-hierarchy.md) | The Core / Distribution / Access design model. |

**↳ [VLANs & Trunks](./layer-2-switching/vlans-and-trunks/)**
| File | Topic |
|------|-------|
| [VLANs Overview](./layer-2-switching/vlans-and-trunks/vlans-overview.md) | Broadcast domains and segmentation. |
| [Trunking & DTP](./layer-2-switching/vlans-and-trunks/trunking-and-dtp.md) | 802.1Q tagging and DTP negotiation. |
| [Voice VLANs](./layer-2-switching/vlans-and-trunks/voice-vlans.md) | QoS, CoS, and IP Phone integration. |
| [VTP Configuration](./layer-2-switching/vlans-and-trunks/vtp-configuration.md) | VLAN synchronization and the revision disaster. |

**↳ [EtherChannel](./layer-2-switching/etherchannel/)**
| File | Topic |
|------|-------|
| [EtherChannel Concepts](./layer-2-switching/etherchannel/etherchannel-concepts.md) | Link aggregation fundamentals. |
| [LACP vs PAgP](./layer-2-switching/etherchannel/lacp-vs-pagp.md) | Open standard vs Cisco proprietary negotiation. |
| [Layer 2 Load Balancing](./layer-2-switching/etherchannel/layer2-load-balancing.md) | Hashing algorithms and the per-flow rule. |

**↳ [Spanning Tree Protocol](./layer-2-switching/spanning-tree-protocol/)**
| File | Topic |
|------|-------|
| [STP Overview](./layer-2-switching/spanning-tree-protocol/stp-overview.md) | BPDUs and broadcast storm prevention. |
| [Root Bridge Election](./layer-2-switching/spanning-tree-protocol/root-bridge-election.md) | BIDs, priorities, and deterministic root placement. |
| [Types of STP](./layer-2-switching/spanning-tree-protocol/types-of-stp/) | 802.1D, PVST+, Rapid-PVST+, and MST. |
| [Edge Port Guards](./layer-2-switching/spanning-tree-protocol/protection-features/edge-port-guards/) | BPDU Guard and BPDU Filter. |
| [Switch Port Guards](./layer-2-switching/spanning-tree-protocol/protection-features/switch-port-guards/) | Loop Guard and Root Guard. |
| [Speed Features](./layer-2-switching/spanning-tree-protocol/speed-features/portfast.md) | PortFast configuration. |

**↳ [Ultimate L2 Lab](./layer-2-switching/ultimate-l2-lab/)**
| File | Topic |
|------|-------|
| [Topology Diagram](./layer-2-switching/ultimate-l2-lab/topology-diagram.md) | Physical and logical map of the lab. |
| [IP & VLAN Schema](./layer-2-switching/ultimate-l2-lab/ip-and-vlan-schema.md) | Subnets, SVIs, and addressing. |
| [Lab Tasks](./layer-2-switching/ultimate-l2-lab/lab-tasks.md) | The step-by-step build challenge. |
| [Troubleshooting Guide](./layer-2-switching/ultimate-l2-lab/troubleshooting-guide.md) | Master diagnostic methodology. |

### 🔹 [Layer 3 Routing](./layer-3-routing/)
*Inter-VLAN communication, path selection, and dynamic routing protocols.*

| File | Topic |
|------|-------|
| [Routing Overview](./layer-3-routing/routing-overview.md) | The RIB, TTL, and Multilayer Switching. |
| [Path Selection](./layer-3-routing/routing-table-path-selection.md) | LPM, Administrative Distance, and Metric. |
| [Static Routing](./layer-3-routing/static-routing.md) | Manual routes, Default routes, and Floating statics. |
| [Inter-VLAN Routing](./layer-3-routing/inter-vlan-routing.md) | SVIs, ROAS, and MAC rewrites. |

**↳ [Dynamic Protocols](./layer-3-routing/dynamic-protocols/)**
| File | Topic |
|------|-------|
| [OSPF](./layer-3-routing/dynamic-protocols/ospf.md) | Link-State, Dijkstra SPF, and Areas. |
| [EIGRP](./layer-3-routing/dynamic-protocols/eigrp.md) | Distance-Vector, DUAL, and Unequal-Cost LB. |
| [BGP](./layer-3-routing/dynamic-protocols/bgp.md) | Path-Vector, ASNs, and Internet Routing. |
| [RIP](./layer-3-routing/dynamic-protocols/rip.md) | Hop-count, Split Horizon, and Route Poisoning. |

### 🔹 [Network Services](./network-services/)
*Essential infrastructure protocols that provide endpoint connectivity and redundancy.*

| File | Topic |
|------|-------|
| [DHCP Operations](./network-services/dhcp.md) | The DORA process, IP pools, and Option 150. |
| [HSRP & VRRP](./network-services/hsrp-vrrp.md) | First Hop Redundancy, Virtual IPs, and Failover. |
| [NAT & PAT](./network-services/nat.md) | Inside/Outside translation and Port Overload. |
| [NTP](./network-services/ntp.md) | Stratum levels, clock synchronization, and logging. |

### 🔹 [Security](./security/)
*Defense-in-depth mechanisms protecting the data, control, and management planes.*

| File | Topic |
|------|-------|
| [Access Control Lists (ACLs)](./security/acls.md) | Standard/Extended filtering and Top-Down logic. |
| [Port Security](./security/port-security.md) | MAC address restriction, Sticky learning, and Violations. |
| [AAA Framework](./security/aaa.md) | RADIUS/TACACS+, Identity, and Role-Based Access. |
| [DHCP Snooping & DAI](./security/dhcp-snooping-dai.md) | Mitigating rogue servers and Man-in-the-Middle ARP attacks. |

---

## 🎯 How to Use This Knowledge Base

1. **Start at the Root:** Read the `reference-guides` to familiarize yourself with the protocols and CLI commands.
2. **Build the Foundation:** Read through `layer-2-switching` sequentially. Do not skip the STP Guards; they are critical for lab stability.
3. **Connect the Networks:** Move to `layer-3-routing` to understand how your isolated VLANs communicate.
4. **Enable Services:** Use `network-services` to deploy DHCP for your endpoints, NAT for internet access, and HSRP for gateway redundancy.
5. **Lock it Down:** Apply the concepts in `security` to protect your edge ports and management planes.
6. **Execute the Lab:** Open `layer-2-switching/ultimate-l2-lab/lab-tasks.md` and build the network in Packet Tracer. Use the `troubleshooting-guide.md` when things break.
