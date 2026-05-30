# MOBAN Chapter 8 — Wireless Ad Hoc Networks
# Comprehensive Study Guide

---

## PART 1: THEORY SUMMARY

---

### 1. Introduction

#### 1.1 Conventional vs. Ad Hoc Wireless Networks

| Feature | Conventional WLAN (Infrastructure) | Ad Hoc / MANET |
|---|---|---|
| Infrastructure | Access point required | None |
| Topology | One-hop star | Multi-hop mesh |
| Administration | Centralized (AP) | Distributed |
| Coverage | Limited by AP range | Extended by multi-hop |
| Routing | Handled by AP/router | Handled by nodes themselves |

**IEEE 802.11 infrastructure mode:** All traffic passes through an access point. Nodes communicate in one hop. The AP handles all administration.

**IEEE 802.11 ad-hoc mode:** Devices in range communicate peer-to-peer without infrastructure. Nodes outside direct range are reachable via multi-hop.

#### 1.2 What is a MANET?

> **Mobile Ad hoc NETwork (MANET):** *A transitory association of mobile nodes that communicate with each other over wireless links and that do not depend upon any fixed support infrastructure (self-creating, self-organizing, self-administering).*

Key defining properties:
- **Self-creating:** no pre-configuration needed to join
- **Self-organizing:** network organizes itself dynamically
- **Self-administering:** no central controller

**Ad hoc** (Latin): *fashioned from whatever is immediately available; formed or used for specific or immediate problems.*

#### 1.3 MANET vs. Mesh Network

| | MANET | Wireless Mesh |
|---|---|---|
| Node roles | All nodes equal | Dedicated mesh routers + client nodes |
| Configuration | Spontaneous, unplanned | More planned |
| Mobility | All nodes mobile | Mesh routers often fixed |
| Scope | Broader term | Subtype of ad hoc |

> A wireless mesh network is a wireless multi-hop ad hoc network subtype with a more planned configuration and specific roles for mesh routers.

#### 1.4 Why MANETs?

Four major use-case motivations:

1. **No infrastructure available** — disaster recovery, search and rescue
2. **Infrastructure too time-consuming to set up** — fast/temporary deployments (military operations, event networking)
3. **Infrastructure too expensive** — rural/remote areas, developing regions
4. **Self-organizing need** — spontaneous collaboration between devices

#### 1.5 Origin and History

- **1970s:** Battlefield communication, US Army (origin of the concept)
- **Mid-1990s:** Commercial radios → academic research community
- **1997:** IETF MANET working group established
- **2000s onward:** Commercial products; mesh networks for internet access, IoT, etc.

#### 1.6 Commercial Use Cases

Commercial deployments tend to be **mesh networks** (not pure device-only MANETs) because:
- Pure MANETs require all end devices to cooperate spontaneously — incentive is unclear
- Mesh networks with dedicated routers are easier to control
- Exception: military, emergency services where cooperation is enforced

Requirements driving commercial interest:
- Fast deployment
- Self-organization and self-maintenance
- Limited configuration effort

---

### 2. Characteristics of Ad Hoc Networks

#### 2.1 Multi-hop Routing
Nodes act as **routers**, relaying traffic for others. Enables connections between nodes outside each other's direct range.

#### 2.2 Dynamic Network Topology
Nodes move freely → variable link capacities and frequent link breaks. Routing must continuously adapt.

#### 2.3 Autonomous Operation
No fixed infrastructure (no DHCP server, DNS, Mobile IP anchor). All functionality must operate in a **distributed manner**.

#### 2.4 Asymmetric Links
- Different antenna characteristics, transmit power, and propagation conditions
- A node A may hear B but B may not hear A
- **Unidirectional links are possible** — routing protocols must handle or avoid them

#### 2.5 Redundant Links
- No control over redundancy
- Causes: large computational overhead for routing updates, increased congestion, broadcasting problems

#### 2.6 Bandwidth-Constrained, Variable Capacity Links
- Shared wireless medium → interference, fading, collisions
- Capacity varies over time and depends on neighboring transmissions

#### 2.7 Limited Physical Security
- Open wireless medium: eavesdropping, traffic injection, denial of service
- Harder to secure than wired networks

#### 2.8 Heterogeneous Devices
- Nodes may have different hardware (CPU, memory, battery)
- Some may be battery-powered → energy is a design constraint

#### 2.9 Network Splitting and Merging
- Mobile nodes may cause a network to partition or two separate networks to merge
- Protocols must handle these events gracefully

---

### 3. Impact on Protocol Design

The challenging environment of MANETs affects **every layer** of the protocol stack:

| Challenge | Protocol Area |
|---|---|
| Fair channel access, hidden/exposed terminals | **MAC protocols** (see Ch. 2) |
| Establishing/maintaining routes between any two nodes | **Routing protocols** |
| Efficiently propagating control info to all nodes | **Broadcasting techniques** |
| Finding services in a dynamic network | **Resource & service discovery** |
| Assigning unique IP addresses without DHCP | **Address assignment** |
| TCP assumes fixed, reliable links — not valid here | **Transport protocols** |
| Accessing the Internet from an ad hoc network | **Internet connectivity** |
| Detecting/preventing attacks | **Security** |
| Preventing nodes from refusing to forward traffic | **Cooperation enforcement** |

---

### 4. Routing in Ad Hoc Networks

#### 4.1 Why Traditional Routing Fails

Standard fixed-network routing protocols (e.g., RIP, OSPF) are designed for:
- Slow topology changes → infrequent updates are sufficient
- High bandwidth for control traffic → large routing tables acceptable

In MANETs these assumptions break:
- Topology changes fast (mobility) → slow convergence = stale routes
- Bandwidth is scarce → large routing overhead is unacceptable

#### 4.2 Routing Challenges

Finding a route from A to B is hard when:
- Links are asymmetric
- Bandwidth for control messages is limited
- Control message propagation is unreliable (wireless broadcast)
- The environment is highly dynamic
- Link capacity varies and link breaks occur frequently
- Computation power is low
- Energy consumption matters
- Security must be guaranteed

#### 4.3 L2 vs. L3 Routing

| | Mesh Under (L2) | Route Over (L3) |
|---|---|---|
| Forwarding based on | MAC (Layer 2) addresses | IP (Layer 3) addresses |
| IP routing inside mesh | No — single broadcast domain | Yes — collection of link-local scopes |
| Example | IEEE 802.11s (HWMP) | OLSR, AODV |

---

### 5. Classification of Routing Protocols

```
Ad hoc routing protocols
├── Proactive (table-driven)
│   ├── Distance vector: DSDV, WRP
│   └── Link state: OLSR, OLSRv2
├── Reactive (on-demand)
│   ├── Source routing: DSR
│   └── Hop-by-hop: AODV, DYMO
├── Hybrid
│   └── ZRP (Zone Routing Protocol)
└── Location-based
    └── LAR, DREAM
```

---

### 6. Proactive Routing Protocols

**Definition:** Maintain up-to-date routes to every possible destination at all times.

**How:** Exchange routing control information throughout the entire network periodically and upon link changes.

**Advantage:** Routes are always ready — no delay when traffic needs to start.

**Disadvantage:** Continuous overhead even when no data is being sent; poor scalability.

#### 6.1 Distance Vector Routing (Background)

Based on the **Distributed Bellman-Ford (DBF)** algorithm (same as RIP for fixed networks).

Each node maintains:
- **Distance table:** distance to each destination via each neighbor
- **Routing table:** best next hop to each destination

Nodes periodically exchange **distance vectors** = `(destination, distance)` with neighbors.

**Problems in MANETs:**
- **Counting to infinity:** When a route breaks, nodes may keep incrementing the metric forever until they agree the route is gone
- **Routing loops:** Temporary inconsistencies cause packets to loop

> Routing anomalies are significantly worse in MANETs due to faster topology changes!

#### 6.2 DSDV — Destination Sequenced Distance Vector

**Type:** Proactive, distance vector

**Key innovation:** Adds **sequence numbers** to routing updates to guarantee loop-free routing.

- Distance vector format: `(destination, metric, sequence_number)`
- Sequence number is maintained by the **destination** node itself
- Ensures all updates are applied in order → no loops

**How DSDV avoids loops:**
- When a route breaks: destination increments its sequence number and advertises `(dest, ∞, seq_odd)`
- Intermediate nodes receiving a higher sequence number immediately discard lower-sequence-number routes
- Old stale routes with lower sequence numbers are ignored, preventing loops

**Two types of updates:**
| Update Type | Content | Frequency |
|---|---|---|
| **Full dump** | All routing info | Infrequent |
| **Incremental** | Only changes since last full dump | Frequent |

**Damping:** Delays advertisement of unstable routes. When a route changes rapidly, DSDV waits before advertising it (stores time between first and best announcement of a path; inhibits update if route seems unstable).

**Drawbacks:**
- Trade-off between overhead and convergence latency
- Parameters (periodic update interval, settling time) must be tuned

#### 6.3 WRP — Wireless Routing Protocol

**Type:** Proactive, distance vector

**Key features:**
- Update messages include `(destination, distance, predecessor)` — the predecessor info helps avoid loops
- Sends routing updates **periodically** and **upon topological changes**
- All nodes maintain hello messages to detect neighbors

**WRP overhead:** Independent of traffic load — each node sends a hello message every 10 seconds regardless of data traffic. For N nodes: `N/10` packets/second.

#### 6.4 OLSR — Optimized Link State Routing Protocol

**Type:** Proactive, link state

**Two key optimizations over standard link state flooding:**

1. **Optimized flooding using Multipoint Relays (MPR)**
2. **Reduced link state info:** Only MPR nodes generate and forward link state information

##### Multipoint Relays (MPR)

**Goal:** Reduce the number of retransmissions during flooding.

**Concept:** Each node selects a subset of its 1-hop neighbors (the MPR set) such that:
- Every **2-hop neighbor** is reachable through at least one MPR
- The MPR set is **minimal** (fewest nodes possible)

**Flooding rule:**
- A node only rebroadcasts a flooding packet if it received it from one of **its MPRs**
- This dramatically reduces duplicate transmissions

**MPR Set Construction Algorithm:**
1. Identify all **2-hop neighbors** of the node
2. Find **forced MPRs**: 1-hop neighbors that are the only path to reach a specific 2-hop neighbor
3. Add those forced MPRs to the MPR set
4. Check which 2-hop neighbors are still uncovered
5. Greedily select the 1-hop neighbor covering the most uncovered 2-hop neighbors
6. Repeat until all 2-hop neighbors are covered

**Example:**
```
Node 1's 1-hop neighbors: {3, 7, 9, 11, 12}
Node 1's 2-hop neighbors: {6, 8, 2, 10, 14, 16, 13, 5}

Forced MPRs:
  Node 12 → only one that reaches {5, 13, 16}
  Node 11 → only one that reaches {10, 14}
  Node 7  → only one that reaches {6, 8}

Remaining uncovered 2-hop NB: {2}
  Reachable via node 3 or node 9

Possible MPR sets: {12, 11, 7, 3} or {12, 11, 7, 9}
```

**Link state reduction:** Only MPR nodes broadcast their link state information (Topology Control messages). Non-MPR nodes do not generate TC messages. This significantly reduces the volume of link state info flooding the network.

##### OLSRv2 (Updated Version)
- Same core algorithms as OLSR
- More flexibility, simplicity, efficiency, modularity
- Supports **link metrics other than hop count**
- Uses stand-alone components: MANET Neighborhood Discovery Protocol (NHDP), generalized TLV-based packet format

---

### 7. Reactive Routing Protocols

**Definition:** Routes are set up **only when needed** and maintained **only as long as needed**.

**How:**
1. **Route discovery:** Broadcast Route Requests (RREQ) — wait for Route Reply (RREP)
2. **Route maintenance:** Keep active routes alive; send Route Errors (RERR) on link breaks

**Advantage:** No overhead for routes that are never used.

**Disadvantage:** Latency before first packet can be sent (route discovery delay).

#### 7.1 AODV — Ad hoc On-demand Distance Vector

**Type:** Reactive, hop-by-hop forwarding

**Three message types:**

| Message | Direction | Purpose |
|---|---|---|
| **RREQ** (Route Request) | Flooded outward | Source requests route to destination |
| **RREP** (Route Reply) | Unicast back to source | Destination (or intermediate node with fresh route) responds |
| **RERR** (Route Error) | Broadcast upstream | Notifies sources of a broken link |

##### RREQ Propagation
1. Source broadcasts RREQ with a unique ID
2. Every intermediate node rebroadcasts the RREQ **exactly once** (identified by source IP + RREQ ID)
3. RREQ records the reverse path implicitly at each node
4. Destination receives RREQ → sends RREP back along reverse path

##### RREP Propagation
1. Destination (or intermediate node with a valid cached route) sends RREP
2. Each node forwarding RREP records the **forward path** entry
3. Source receives RREP → data flow can begin

##### Routing Tables in AODV
- Only **active routes** are kept in routing tables
- Unused entries **time out** automatically
- Routing table entry: `(destination, next-hop, sequence-number, lifetime)`

##### Route Error (RERR)
1. Node detects a link break (neighbor gone silent)
2. Broadcasts RERR to all sources using that link
3. Sources initiate a new route discovery

##### Control Packet Count for AODV
- **RREQ:** Broadcast by every node except the destination → **(N-1) RREQs** for N nodes
- **RREP:** Unicast, forwarded by every node on the path from destination to source → **h RREPs** for a path of h hops

**Total overhead per route discovery:** `(N-1) + h` control packets

##### AODV Expanding Ring Search
Optimization to reduce overhead when destination is nearby:

1. **Phase 1:** Send RREQ with `TTL = 2` (reaches only 1-hop and 2-hop neighbors)
   - If destination found within 2 hops → RREP received, done
   - Control overhead: `1 + (number of 1-hop neighbors)` RREQs
2. **Phase 2:** If no RREP received, send RREQ with `TTL > max hops` (floods entire network)

**When expanding ring search helps:** When many destinations are close (within TTL). For distant destinations, it actually adds overhead (two phases instead of one).

#### 7.2 DSR — Dynamic Source Routing

**Type:** Reactive, source routing

**Key difference from AODV:** The **full route is stored in every data packet** (source routing). Intermediate nodes don't maintain routing tables.

##### Route Discovery
1. Source broadcasts RREQ with a **Route Record (RR)** — initially empty
2. Each intermediate node appends its own address to the RR before rebroadcasting
3. RREQ propagation stops when:
   - Node already received this RREQ (discard)
   - Node's address already in the RR (loop detected)
4. Destination receives RREQ → sends RREP with the complete route

##### Route Reply
- Destination sends RREP containing the full route from RREQ's RR
- RREP is forwarded along the **reverse route** (if links are bidirectional)
- If links are unidirectional: piggyback RREP in a new RREQ in the reverse direction

##### Packet Forwarding
- Each data packet carries the full list of intermediate nodes
- Intermediate nodes simply read the next hop from the source route in the packet

##### DSR Optimizations
- **RREQ hop limits:** Limit RREQ propagation (like AODV's TTL)
- **Route caching:** Nodes cache routes overheard from passing RREQ/RREP packets → reduces discovery overhead
- **Delay RREP:** Prevents RREP storms when multiple nodes respond simultaneously

#### 7.3 DYMO — Dynamic MANET On-demand Routing

- Successor of AODV (also known as AODVv2)
- Same algorithms as AODV, slightly simplified implementation
- **Path accumulation feature:** Nodes append their info to RREQ/RREP, giving better topology knowledge
- Uses a generalized packet/message format (compatible with OLSR message format)

---

### 8. Hybrid Routing: ZRP

**ZRP — Zone Routing Protocol**

**Concept:** Combine proactive and reactive approaches using a **zone radius** parameter (e.g., r = 2 hops).

| Inside the zone (≤ r hops) | Outside the zone (> r hops) |
|---|---|
| **Proactive intrazone routing** | **Reactive interzone routing** |
| Routes maintained at all times | Routes established on demand |
| Low latency for nearby nodes | Low overhead for distant nodes |

**Interzone routing** uses **bordercasting**: send route requests to border nodes of the zone, which relay them into adjacent zones.

**Trade-off:** Zone radius r must be tuned. Larger r → more proactive overhead; smaller r → more reactive latency.

---

### 9. Hierarchical Routing: Clustering

**Goal:** Improve scalability by introducing hierarchy.

**How:**
- Network is partitioned into **non-overlapping clusters**
- Each cluster has one **cluster head**
- Cluster heads take a leading role in disseminating routing information
- **Gateway nodes** connect adjacent clusters

**Example:** RREQs are only forwarded by cluster heads via gateway nodes — dramatically reduces flooding scope.

---

### 10. Location-Based Routing

**Concept:** Use physical location information to **limit flooding** of control messages.

**How it works:**
1. A **location information service** provides nodes with the position of the destination
2. Control packets (or data packets) are forwarded only toward the destination's direction

**Three types of location services:**
| Type | How |
|---|---|
| **Proactive location database** | Specific nodes act as databases; nodes register/query position |
| **Proactive location dissemination** | All nodes periodically broadcast their current location |
| **Reactive location systems** | Position is queried on-demand (like reactive routing) |

#### 10.1 DREAM — Distance Routing Effect Algorithm for Mobility

- Nodes periodically broadcast their physical location
- **Distance effect:** Far-away nodes move at lower apparent angular speed → need fewer updates (packets have a lifetime proportional to distance)
- **Speed effect:** Faster-moving nodes broadcast more frequently
- Data packets are **partially flooded** toward the cone in the direction of the destination

#### 10.2 LAR — Location Aided Routing

**Reactive protocol** that uses location information to limit the RREQ flood area.

**Two schemes:**

**Scheme 1 — Expected Zone:**
- Source calculates an **expected zone** for destination D based on last known position P₀ at time t₀ and estimated speed
- Expected zone is a circle of radius `r = (t₁ - t₀) × speed_estimate` around P₀
- RREQ is only rebroadcast by nodes within a **request zone** that encloses the expected zone

**Scheme 2 — Distance-Based:**
- Source calculates its distance to the destination and puts it in the RREQ
- Only nodes that are **closer to the destination** than the source relay the RREQ
- Forward condition: `DSTD(relay) < DSTD(source) + Δ`

---

### 11. Routing Metrics

#### 11.1 Metrics to Minimize (lower = better)

| Metric | Description |
|---|---|
| **Hop count (h)** | Number of hops; simplest metric. Favors long-range but often less stable links |
| **ETX — Expected Transmission Count** | Expected number of transmissions needed for a packet to be received error-free at the destination. Calculated from forward and reverse delivery ratios. Range: [1, ∞) |
| **Loss rate** | Fraction of packets lost on a link |
| **Delay** | Transmission + propagation + queuing delay |
| **Congestion** | Number of neighbors that can overhear a transmission (interference proxy) |

#### 11.2 Metrics to Maximize (higher = better)

| Metric | Description |
|---|---|
| **Bandwidth** | Available throughput on the path (bottleneck link determines path bandwidth) |
| **Stability** | Connectivity lifetime of links |
| **Battery runtime** | Remaining energy at forwarding nodes |

#### 11.3 ETX Detail

**ETX for a link** = `1 / (forward_delivery_ratio × reverse_delivery_ratio)`

**ETX for a path** = sum of ETX values for each link on the path

**Example:**
```
Link predicted throughput:  100%  50%  50%  33%  20%
ETX for link:               1     2    2    3    5
Path ETX:                   1 + 2 + 2 + 3 + 5 = 13
```

> A path with 5 long-range unreliable links can have a higher ETX than a path with more shorter, reliable hops.

#### 11.4 Combined Cost Function

```
Cost = α·h + β·i + γ·d + δ·r
```
where:
- `h` = hop count
- `i` = interference
- `d` = delay
- `r` = some reliability metric
- `α, β, γ, δ` = weights

#### 11.5 Performance Metrics for Comparing Protocols

- **Packet delivery ratio** (PDR)
- **Routing overhead** (control packets / data packets)
- **End-to-end delay**
- **Throughput**

> **Key insight:** Every MANET protocol can outperform the others in a specific networking context. Performance depends heavily on: network size, density, traffic, mobility, link asymmetry, etc. There is no universally best protocol.

---

### 12. Broadcasting in MANETs

#### 12.1 Why Broadcasting is Needed

Broadcasting is used for:
- RREQ propagation (reactive protocols)
- Link state flooding (proactive protocols)
- Hello messages (neighbor discovery)

Naive approach: **Blind flooding** — every node rebroadcasts every packet it receives once.

#### 12.2 The Broadcast Storm Problem

**Problem:** In dense networks, blind flooding causes massive redundancy:
- When A broadcasts, both B and C receive it
- B and C both rebroadcast at nearly the same time → **collision**
- Node D receives the same packet multiple times (from B and from C) — only one copy was needed
- In a large network: exponential explosion of rebroadcasts

**Consequences:**
- Network cannot transport normal traffic
- Severe medium contention and collision
- This is called the **broadcast storm problem**

#### 12.3 Broadcast Solutions

| Technique | How |
|---|---|
| **Random jitter** | Add a random delay before rebroadcasting → desynchronizes transmissions, reduces simultaneous collisions |
| **Counter-based** | Node only rebroadcasts if it has received fewer than k copies (rebroadcasting becomes less likely as copies accumulate) |
| **Distance-based** | Only rebroadcast if the sender is far away (large coverage gain) |
| **Location-based** | Only rebroadcast if the additional coverage area is large |
| **2-hop neighbor-based (MPR)** | Only designated MPR nodes rebroadcast — the OLSR approach |

> The **MPR approach** is the most principled solution: it guarantees that all 2-hop neighbors are covered while minimizing retransmissions.

---

### 13. Underlying Technologies

#### 13.1 IEEE 802.11s (WLAN Mesh Standard)

- Integrated mesh networking solution extending the **IEEE 802.11 MAC** standard
- Supports unicast, multicast, and broadcast over **self-configuring multi-hop topologies**
- **Layer 2 mesh:** the mesh appears as a single broadcast domain to higher layers
- Runs on top of 802.11a/b/g/n physical layers
- **Default routing protocol:** HWMP (Hybrid Wireless Mesh Protocol) — based on AODV combined with tree-based proactive routing
- Other routing protocols can optionally be supported
- Used in: One Laptop per Child (OLPC) project, open80211s (Linux kernel)

#### 13.2 IEEE 802.15.4 Multi-hop Networks (IoT)

- Used for low-power, low-rate IoT/sensor networks
- **Routing mechanism:** RPL — establishes a **DODAG** (Destination Oriented Directed Acyclic Graph)
- DODAG provides a path from every node to a root router (**MP2P traffic = upward traffic**)
- Nodes have a **parent–child relationship** in the DAG
- Multiple parents provide redundancy
- P2P traffic (between two sensors) is supported but more complex

#### 13.3 BLE Meshing

BLE (Bluetooth Low Energy) supports two communication modes:
| Mode | Description |
|---|---|
| **Broadcaster-Observer** | No connections; uses 3 advertising channels; one device broadcasts, others observe |
| **Central-Peripheral** | Connection-based; hops over 37 data channels |

**Two BLE meshing flavors:**

**Rebroadcasting mesh (connection-less):**
- Flood all messages to all nodes via broadcasts
- Need to control flooding (broadcast storm problem)
- Example: Nordic nRF OpenMesh

**Spanning tree mesh (connection-based):**
- Broadcasting used in startup phase to discover neighbors
- Constructs a spanning tree; each node has up to 7 connections (6 as central, 1 as peripheral)
- Example: Fruitymesh

---

### 14. Summary: Protocol Comparison

| Protocol | Type | Subtype | Key Idea | Overhead | Route Latency |
|---|---|---|---|---|---|
| **DSDV** | Proactive | Distance vector | Sequence numbers for loop-free routing | High (always) | Zero |
| **WRP** | Proactive | Distance vector | Predecessor info to avoid loops | High (always) | Zero |
| **OLSR** | Proactive | Link state | MPR reduces flooding overhead | Medium-High | Zero |
| **AODV** | Reactive | Hop-by-hop | RREQ flood + RREP unicast | Low when idle | On first packet |
| **DSR** | Reactive | Source routing | Route stored in packet header | Low when idle | On first packet |
| **DYMO** | Reactive | Hop-by-hop | AODV successor with path accumulation | Low when idle | On first packet |
| **ZRP** | Hybrid | — | Proactive inside zone, reactive outside | Tunable | Low nearby |
| **LAR** | Location-based | Reactive | Limit RREQ flood to expected zone | Low | On first packet |
| **DREAM** | Location-based | Proactive | Directional partial flood | Medium | Zero |

**When to prefer proactive (e.g., WRP/OLSR):**
- High connection frequency
- Nodes communicate often with many destinations
- Delay budget is tight

**When to prefer reactive (e.g., AODV):**
- Sparse traffic (few connections set up per unit time)
- Destination may not be needed → avoid maintaining unnecessary routes
- Network is large but sparsely used

---

## PART 2: EXERCISES AND SOLUTIONS

---

### Exercise 1: AODV / OLSR / WRP — 16-Node Network

#### Network Setup

A 16-node wireless ad hoc network with link speeds given in Mbps.

**Link speed to delay conversion:**

| Link speed (Mbps) | Data packet (1000 bytes = 8000 bits) | Control packet (500 bytes = 4000 bits) |
|---|---|---|
| 1 | 8 ms | 4 ms |
| 2 | 4 ms | 2 ms |
| 4 | 2 ms | 1 ms |
| 8 | 1 ms | 0.5 ms |

**Assumptions:**
- Propagation and processing delays = 0
- All links bidirectional (symmetric)
- No transmission errors
- Data packets: 1000 bytes; Control packets: 500 bytes

---

#### Part I: AODV — Node 6 → Node 16, Metric = Delay

**Key AODV principle:** The RREQ that **arrives first** at the destination determines the path (metric = delay).

**Q1: What is the resulting path?**

Convert all link speeds to per-packet delays, then find the path from 6 to 16 with **minimum total control-packet delay** (RREQ uses control-packet size).

**Resulting path: 6 → 3 → 9 → 1 → 11 → 14 → 15 → 16**

Control packet delays along this path:
- 6→3: 2 ms (4 Mbps link)
- 3→9: 0.5 ms (8 Mbps link)
- 9→1: 1 ms (4 Mbps link)
- 1→11: 1 ms (4 Mbps link)
- 11→14: 1 ms (4 Mbps link)
- 14→15: 1 ms (4 Mbps link)
- 15→16: 0.5 ms (8 Mbps link)
- **Total RREQ travel time: 7 ms**

**Q2: How long does it take to set up the connection?**

- RREQ arrives at node 16 after **7 ms**
- RREP travels back along the same path, also **7 ms**
- **Total setup time: 14 ms**

**Q3: How many control packets are needed?**

- **RREQ:** Broadcast by every node except the destination → **15 RREQs** (16 nodes − 1)
- **RREP:** Unicast, forwarded by every node on the path from 16 back to 6
  - Path = 6→3→9→1→11→14→15→16 → nodes 16, 15, 14, 11, 1, 9, 3 forward the RREP → **7 RREPs**
- **Total: 22 control packets**

**Q4: Routing tables of nodes 1 and 12 after a long time?**

- **Node 12:** Not on the path → routing table remains **empty** (AODV only keeps entries for active routes)
- **Node 1:** On the path → two entries:
  - Destination 6, next hop = 9
  - Destination 16, next hop = 11

**Q5: Path if all links had the same delay?**

When all link delays are equal, the RREQ that arrives first is the one that traversed the **fewest hops**.

There are **4 paths of 4 hops** between node 6 and node 16. All arrive simultaneously. The actual path chosen would depend on processing delays and MAC behavior (non-deterministic in this idealized model).

---

#### Part II: OLSR — Node 6 → Node 16

**Q1: Path with metric = hop count?**

All 4 shortest paths have **4 hops**. Any of them is valid. Example: **6 → 7 → 1 → 12 → 16**

Metric = 4 hops

**Q2: Path with metric = bandwidth?**

Bandwidth of a path = **minimum link bandwidth** along the path (bottleneck).

Goal: **maximize the minimum link bandwidth**.

Best path: **6 → 7 → 8 → 5 → 12 → 1 → 11 → 14 → 15 → 16**

- All links on this path have bandwidth ≥ 4 Mbps
- **Path metric (bandwidth) = 4 Mbps**

> Note: This path has more hops (9 hops) than the hop-count-optimal path (4 hops), but achieves higher bandwidth.

**Q3: MPR set of node 1**

Step-by-step MPR construction for node 1:

1. **1-hop neighbors of 1:** {3, 7, 9, 11, 12}
2. **2-hop neighbors of 1:** {6, 8, 2, 10, 14, 16, 13, 5}
3. **Forced MPRs** (only neighbor that can reach a given 2-hop neighbor):
   - 2-hop NB {5, 13, 16}: only reachable via **12** → 12 is a forced MPR (covers {5, 13, 16})
   - 2-hop NB {10, 14}: only reachable via **11** → 11 is a forced MPR (covers {10, 14})
   - 2-hop NB {6, 8}: only reachable via **7** → 7 is a forced MPR (covers {6, 8})
4. **Remaining uncovered 2-hop NB:** {2}
   - Reachable via **3** or **9** (either works)
5. **Possible minimal MPR sets:**
   - `{12, 11, 7, 3}` or `{12, 11, 7, 9}`

---

#### Part III: AODV vs. WRP Overhead (f connections/second)

**Setup:**
- Node 1 = server
- Nodes 2, 8, 12 each initiate f connections/second to node 1
- AODV: each connection requires a full RREQ/RREP cycle; metric = delay
- WRP: converged; only hello messages every 10 seconds per node; 16 nodes total

**Optimal paths (metric = delay) for each source:**

| Source | Path to node 1 | Hops |
|---|---|---|
| Node 2 | 2 → 9 → 1 | 2 |
| Node 8 | 8 → 5 → 12 → 1 | 3 |
| Node 12 | 12 → 1 | 1 |

**AODV overhead:**

Per connection per source (RREQ broadcasts entire network = 15 RREQs):

| Source | RREQs | RREPs | Total/connection |
|---|---|---|---|
| Node 2 | 15 | 2 | 17 |
| Node 8 | 15 | 3 | 18 |
| Node 12 | 15 | 1 | 16 |
| **Total per 3 connections** | 45 | 6 | **51** |

**AODV overhead = 51·f packets/second**

**WRP overhead:**

- Each of 16 nodes sends a hello every 10 seconds
- **WRP overhead = 16/10 = 1.6 packets/second** (independent of f)

**Q1: At what f do they have the same overhead?**

```
51·f = 1.6
f = 1.6/51 ≈ 0.0314 connections/second ≈ 1.88 connections/minute
```

**Q2: Which is best for large f?**

For large f, **WRP is better** because:
- AODV overhead grows linearly with f (51·f)
- WRP overhead is constant (1.6 pkt/s), independent of f

**Q3: AODV with Expanding Ring Search (TTL = 2)**

With TTL = 2, the RREQ only reaches 1-hop and 2-hop neighbors. Nodes 2, 8, and 12 can all reach server 1 in ≤ 2 hops, so the first phase succeeds.

| Source | Phase 1 RREQs | RREPs |
|---|---|---|
| Node 2 | 3 (nodes 2, 3, 9) | 2 |
| Node 8 | 3 (nodes 8, 7, 5) | 2 |
| Node 12 | 4 (nodes 12, 5, 13, 16) | 1 |
| **Total** | 10 | 5 = **15 total** |

**Expanded ring search overhead = 15·f_exp packets/second**

**How many more connections for the same overhead?**
```
15·f_exp = 51·f
f_exp / f = 51/15 = 3.4
```
→ **3.4× more connections** can be supported with the same control packet overhead.

---

### Exercise 2: AODV Normal vs. Expanding Ring Search — 11-Node Network

#### Network Setup
- 11 nodes, 4 connections: 1→11, 4→5, 7→9, 2→10
- No packet loss, AODV routing

**Question:** Which technique has lower overhead — normal RREQ or expanding ring search (TTL = 2)?

#### Normal RREQ Propagation
- Each RREQ is rebroadcast by every node except the destination → **(N-1) = 10 RREQs per destination**
- 4 destinations → **40 RREQs total**

#### Expanding Ring Search (TTL = 2)

Rules:
- If destination within 2 hops: only Phase 1 (TTL=2) needed
- If destination more than 2 hops away: Phase 1 + Phase 2 (full flood)

Phase 1 RREQs = 1 (source) + (number of 1-hop neighbors of source, which rebroadcast)

| Connection | Path length | Phase 1 | Phase 2 | Total |
|---|---|---|---|---|
| 1 → 11 | 4 hops | 1 + 2 = 3 | 10 | 13 |
| 4 → 5 | 1 hop | 1 + 2 = 3 | — | 3 |
| 7 → 9 | 2 hops | 1 + 2 = 3 | — | 3 |
| 2 → 10 | 3 hops | 1 + 3 = 4 | 10 | 14 |
| **Total** | | | | **33** |

**Conclusion:** Expanding ring search = **33 RREQs** vs. normal = **40 RREQs**.

**Expanding ring search is better** for this network because many routes are short (1-2 hops) and don't need the full flood.

> **Key insight:** Expanding ring search is most beneficial when many destinations are close. If most destinations are far away, the two-phase approach actually adds overhead (you pay for both phases).

---

### Exercise 3: OLSR MPR — 25-Node Network

#### Network Setup
- 25 nodes, OLSR routing
- Find a **minimal MPR set for node 1**

#### Solution: Step-by-Step

**Node 1's 1-hop neighbors:** {4, 5, 7, 8, 9, 10, 11, 13, 15, 16, 18, 22}

**Node 1's 2-hop neighbors:** {6, 12, 14, 17, 19, 20, 21, 23, 24, 25, ...} *(all nodes reachable in exactly 2 hops)*

**Step 1 — Find forced MPRs** (only 1-hop neighbor of 1 that can reach a given 2-hop neighbor):

| 2-hop NB forced via | Forced MPR | Covers |
|---|---|---|
| Node 12 | Only reachable via ... | Identify from topology |
| Node 22 | Forced MPR | From 1's neighbors |

**Step 2 — Identify remaining uncovered 2-hop neighbors after forced MPRs are added**

**Step 3 — Greedily select additional 1-hop neighbors** to cover remaining 2-hop neighbors with fewest extra nodes

**Possible MPR sets (8 valid solutions of size 5):**
```
{4, 22, 5, 13, 18}
{4, 22, 5, 15, 20}
{4, 22, 5, 15, 18}
{4, 22, 5, 15, 16}
{4, 22, 10, 13, 18}
{4, 22, 10, 15, 20}
{4, 22, 10, 15, 18}
{4, 22, 10, 15, 16}
```

**Key observations:**
- Nodes 4 and 22 are **always forced MPRs** (forced because they are the only path to certain 2-hop neighbors)
- Either node 5 or 10 must be chosen (to reach 2-hop NB node 6)
- Either node 13 with 18, or node 15 with {16, 18, or 20}, must be added to cover remaining 2-hop neighbors
- **No single solution** — there are 8 valid minimal MPR sets, each of size 5 out of 12 total 1-hop neighbors

---

## PART 3: EXAM PREPARATION — KEY QUESTIONS

The following questions are taken directly from the slides as study prompts:

### Conceptual Questions

1. **Why is routing in ad hoc networks different from routing in fixed networks?**
   - Fast topology changes → slow convergence of traditional protocols
   - Scarce bandwidth → high overhead of link state flooding is unacceptable
   - Unreliable broadcast medium for control messages
   - Low power, asymmetric links, mobile nodes

2. **Give the main classes of ad hoc routing protocols and a one-sentence description of each.**
   - **Proactive:** Maintains routes to all destinations at all times via continuous exchange of control info
   - **Reactive:** Sets up routes on demand when communication is needed
   - **Hybrid:** Uses proactive routing within a zone and reactive routing outside it
   - **Location-based:** Uses physical location information to limit where control messages are propagated

3. **Describe two strategies for reactive routing. Give an example for each.**
   - **Source routing (DSR):** The full route is stored in every packet; intermediate nodes follow the stored route without maintaining tables
   - **Hop-by-hop routing (AODV):** Each node maintains its own routing table; packets carry only the destination address and each node decides the next hop

4. **Describe two strategies for proactive routing. Give an example for each.**
   - **Distance vector (DSDV, WRP):** Nodes exchange distance vectors with neighbors; each node maintains distances to all destinations
   - **Link state (OLSR):** Nodes flood link state information to build a global topology map and run Dijkstra locally

5. **What is a routing metric?**
   A routing metric is a value used to evaluate the quality of a path. It guides the selection of the best route. Examples: hop count, ETX, delay, bandwidth, stability.

6. **Describe 2 broadcasting strategies in MANETs.**
   - **Random jitter:** Nodes add a random delay before rebroadcasting to desynchronize transmissions and reduce collisions
   - **MPR-based:** Only multipoint relay (MPR) nodes rebroadcast, ensuring all 2-hop neighbors are covered with minimal redundancy

7. **Explain: MPR, source routing**
   - **MPR:** A subset of a node's 1-hop neighbors chosen such that every 2-hop neighbor is covered; used in OLSR to optimize flooding
   - **Source routing:** A routing approach (used in DSR) where the sender specifies the complete path in the packet header; intermediate nodes do not need routing tables

8. **Give an example use case and explain why ad hoc networking/meshing is useful.**
   - *Disaster recovery:* After an earthquake, infrastructure is destroyed. First responders can form a MANET with their devices, enabling communication without relying on base stations or internet access
   - *Military operations:* Soldiers in the field need a self-organizing network that works without fixed infrastructure and reconfigures automatically when soldiers move

---

### Quick-Reference Formulas

| Quantity | Formula |
|---|---|
| Link delay (data) | `8000 bits / link_speed_in_bps` |
| Link delay (control) | `4000 bits / link_speed_in_bps` |
| AODV RREQs (full flood) | `N − 1` (N = total nodes) |
| AODV RREPs | `h` (h = hops in path) |
| AODV total overhead per connection | `(N−1) + h` |
| WRP overhead | `N / T_hello` packets/second |
| ETX for a link | `1 / (p_forward × p_reverse)` |
| ETX for a path | `Σ ETX_link` over all links |
| AODV overhead (f connections/s) | `(Σ over all sources: (N−1) + h_i) × f` |
| Connection equivalence (ring search) | `f_normal × overhead_normal = f_ring × overhead_ring` |

---

*Study guide created for MOBAN Chapter 8: Wireless Ad Hoc Networks — Jeroen Hoebeke, UGent IDLab*
