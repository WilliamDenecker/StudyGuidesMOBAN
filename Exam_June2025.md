# MOBAN — Exam June 2025
# Exercise 1: Linear Programming — Simplex Algorithm

---

## Problem

```
max z = x1 + 3x2 − 4x3 + x4
s.t.   x1 + x2        ≤ 7
              x3       ≤ 9
       x1 − x2 + x4   ≤ 5
       x1, x2, x3, x4 ≥ 0
```

---

## Step 1: Convert to Standard Form

Add slack variables s1, s2, s3 ≥ 0 to turn each inequality into an equality:

```
x1 + x2              + s1           = 7
        x3                 + s2      = 9
x1 − x2 + x4               + s3     = 5
```

**Initial Basic Feasible Solution (BFS):**
- Basic variables: s1 = 7, s2 = 9, s3 = 5
- Non-basic (= 0): x1 = 0, x2 = 0, x3 = 0, x4 = 0
- z = 0

Write the objective row as: **z − x1 − 3x2 + 4x3 − x4 = 0**

(Positive z-row coefficient = increasing that variable hurts z → don't enter. Negative = beneficial → enter.)

> **Note on x3:** Its z-row coefficient is already **+4** in the initial tableau → increasing x3 decreases z → x3 = 0 throughout all iterations.

---

## Initial Tableau

| BV | x1 | x2     | x3 | x4 | s1 | s2 | s3 | RHS |
|----|----|----|----|----|----|----|----|----|
| s1 | 1  | **1**  | 0  | 0  | 1  | 0  | 0  | 7  |
| s2 | 0  | 0      | 1  | 0  | 0  | 1  | 0  | 9  |
| s3 | 1  | −1     | 0  | 1  | 0  | 0  | 1  | 5  |
| z  | −1 | **−3** | 4  | −1 | 0  | 0  | 0  | 0  |

---

## Iteration 1

**Entering variable:** x2 — most negative z-row coefficient (−3)

**Min-ratio test** (only rows with positive coefficient in the x2 column):

| Row | RHS | x2 coeff | Ratio     |
|-----|-----|----------|-----------|
| s1  | 7   | 1        | 7/1 = **7** ← min |
| s2  | 9   | 0        | — (skip)  |
| s3  | 5   | −1       | — (negative, skip) |

**Leaving variable:** s1 | **Pivot element:** row 1, column x2, value = 1

**Row operations:**
```
New x2-row  = Row1 ÷ 1              (no change, pivot = 1)
New s2-row  = Row2 − 0 × Row1      (no change, x2 coeff = 0)
New s3-row  = Row3 − (−1) × Row1   = Row3 + Row1
New z-row   = z-row − (−3) × Row1  = z-row + 3 × Row1
```

**Tableau after Iteration 1:**

| BV | x1 | x2 | x3 | x4     | s1 | s2 | s3 | RHS |
|----|----|----|----|----|----|----|----|----|
| x2 | 1  | 1  | 0  | 0      | 1  | 0  | 0  | 7  |
| s2 | 0  | 0  | 1  | 0      | 0  | 1  | 0  | 9  |
| s3 | 2  | 0  | 0  | **1**  | 1  | 0  | 1  | 12 |
| z  | 2  | 0  | 4  | **−1** | 3  | 0  | 0  | 21 |

Current BFS: x2 = 7, s2 = 9, s3 = 12, **z = 21**

---

## Iteration 2

**Entering variable:** x4 — only remaining negative z-row coefficient (−1)

**Min-ratio test:**

| Row | RHS | x4 coeff | Ratio       |
|-----|-----|----------|-------------|
| x2  | 7   | 0        | — (skip)    |
| s2  | 9   | 0        | — (skip)    |
| s3  | 12  | 1        | 12/1 = **12** ← only option |

**Leaving variable:** s3 | **Pivot element:** row 3, column x4, value = 1

**Row operations:**
```
New x4-row  = Row3 ÷ 1              (no change, pivot = 1)
New x2-row  = Row1 − 0 × Row3      (no change, x4 coeff = 0)
New s2-row  = Row2 − 0 × Row3      (no change, x4 coeff = 0)
New z-row   = z-row − (−1) × Row3  = z-row + Row3
```

**Tableau after Iteration 2 — OPTIMAL:**

| BV | x1    | x2 | x3    | x4 | s1    | s2 | s3    | RHS    |
|----|----|----|----|----|----|----|----|----|
| x2 | 1     | 1  | 0     | 0  | 1     | 0  | 0     | 7      |
| s2 | 0     | 0  | 1     | 0  | 0     | 1  | 0     | 9      |
| x4 | 2     | 0  | 0     | 1  | 1     | 0  | 1     | 12     |
| z  | **4** | 0  | **4** | 0  | **4** | 0  | **1** | **33** |

All z-row coefficients ≥ 0 → **no further improvement possible → OPTIMAL.**

---

## Optimal Solution

| Variable | Value |
|----------|-------|
| x1       | 0     |
| x2       | **7** |
| x3       | 0     |
| x4       | **12** |
| **z\***  | **33** |

---

## Verification

```
z = x1 + 3x2 − 4x3 + x4
  = 0 + 3(7) − 4(0) + 12
  = 21 + 12
  = 33 ✓

Constraint 1:  x1 + x2       = 0 + 7 = 7  ≤ 7  ✓  (tight)
Constraint 2:  x3             = 0          ≤ 9  ✓
Constraint 3:  x1 − x2 + x4  = 0 − 7 + 12 = 5  ≤ 5  ✓  (tight)
```

---

## Intuition Behind the Solution

| Variable | Why this value? |
|----------|----------------|
| x2 = 7  | Coefficient +3 in objective (highest) → push to max. Constraint 1 limits it to 7. |
| x4 = 12 | Coefficient +1 in objective → push to max. With x2 = 7, constraint 3 allows x4 up to 0 − 7 + x4 ≤ 5, so x4 ≤ 12. |
| x1 = 0  | Positive objective coefficient (+1), but entering x1 would force x2 or x4 to decrease — net effect negative in the final tableau (reduced cost = −4). |
| x3 = 0  | Coefficient −4 in objective → increasing x3 always hurts z; never enters the basis. |

---

## Summary: Simplex Pivots

| Iteration | Entering | Leaving | Pivot row | z value |
|-----------|----------|---------|-----------|---------|
| Initial   | —        | —       | —         | 0       |
| 1         | x2       | s1      | Row 1     | 21      |
| 2         | x4       | s3      | Row 3     | 33      |
| **Optimal** | —      | —       | —         | **33**  |

---

# Exercise 2: EPON — IPACT Limited DBA

## System Parameters

| Parameter | Value |
|-----------|-------|
| N (ONUs) | 8 |
| Distance OLT–ONU (d) | 10 km |
| W_max | 10,000 bytes |
| Guard time (T_g) | 2 µs |
| Buffer size | 1 MB |
| Max Ethernet frame | 1,518 bytes |
| REPORT / GATE size | 64 bytes |
| Upstream line rate (R) | 2 Gbps |
| Traffic | Uniform load, memoryless (Poisson) |

**Pre-computed constants:**

```
v = 300,000 km/s / 1.5 = 200,000 km/s   (speed in optical fiber)
RTT = 2 × 10 / 200,000 = 100 µs
T_Wmax = 10,000 × 8 / 2×10⁹ = 40 µs
T_ctrl = 64 × 8 / 2×10⁹ = 0.256 µs   (REPORT or GATE transmission time)
```

---

## (a) Minimal Cycle Length

The minimum possible cycle is RTT-limited: the OLT cannot send the next GATE until it has received the REPORT from the current cycle, which requires one full round trip.

```
T_cycle_min = RTT + T_report + T_gate
            = 100 + 0.256 + 0.256
            ≈ 100.5 µs
```

---

## (b) Average Cycle Length at 95 % Load

Using the load-balance equation for IPACT Limited (Gated-style derivation):

- Cycle structure: `T = N × (X + T_report + T_g)` where X = data time per ONU
- Load definition: `L = N × X / T` → `N × X = L × T`
- Substituting: `T = L×T + N×(T_report + T_g)` → `T(1−L) = N×(T_report + T_g)`

```
T_cycle = N × (T_report + T_g) / (1 − L)
        = 8 × (0.256 + 2) / (1 − 0.95)
        = 8 × 2.256 / 0.05
        = 18.048 / 0.05
        ≈ 361 µs
```

Check: 361 µs > T_cycle_min = 100.5 µs ✓

---

## (c) Average PON Packet Delay at 95 % Load

The average PON packet delay = queuing/waiting time at ONU + propagation from ONU to OLT.

**Average waiting time** in an IPACT polling system (packet arrives uniformly within the cycle):

```
W_avg = 1.5 × T_cycle = 1.5 × 361 = 541.5 µs
```

**One-way propagation delay:**

```
T_prop = d / v = 10 / 200,000 = 50 µs
```

**Total average PON packet delay:**

```
D_avg = W_avg + T_prop + T_Wmax/2 = 541.5 + 50 + 20 ≈ 612 µs
```

---

## (d) Highest Achievable Load for W_max = 5,000 bytes

When W_max is capped at 5,000 bytes, every ONU can transmit at most W_max bytes per cycle. The system reaches saturation when all ONUs always fill their window:

```
T_Wmax_new = 5,000 × 8 / 2×10⁹ = 20 µs

L_max = T_Wmax / (T_Wmax + T_report + T_g)
      = 20 / (20 + 0.256 + 2)
      = 20 / 22.256
      ≈ 89.9 %
```

> **Reducing W_max from 20,000 → 5,000 bytes lowers the maximum achievable load from ~97 % to ~89.9 %.** A smaller window means the guard-time and control overhead represent a larger fraction of each slot, reducing efficiency.

---

# Exercise 3: Wi-Fi 7 — Maximum Physical Bitrate

## System Parameters

| Parameter | Value |
|-----------|-------|
| Modulation | 4096-QAM → 12 bits/subcarrier |
| Coding rate | 5/6 |
| OFDM symbol time | 12.8 µs |
| Guard Interval options | 0.8 µs, 1.6 µs, 3.2 µs |
| OFDMA | 4 × 980-tone RU (320 MHz band) |
| Spatial streams | 8 |

## (a) Maximum Physical Bitrate

For maximum bitrate, choose the **shortest Guard Interval = 0.8 µs**.

**Total OFDM symbol duration:**

```
T_symbol = 12.8 + 0.8 = 13.6 µs
```

**Total data subcarriers:**

```
N_data = 4 RUs × 980 tones = 3,920 subcarriers
```

**Bits per OFDM symbol** (across all streams):

```
bits/symbol = N_data × bits/subcarrier × coding_rate × N_SS
            = 3,920 × 12 × (5/6) × 8
            = 3,920 × 10 × 8
            = 313,600 bits/symbol
```

**Maximum physical bitrate:**

```
R_max = bits/symbol / T_symbol
      = 313,600 / (13.6 × 10⁻⁶)
      ≈ 23.06 Gbps
```

| Guard Interval | T_symbol | Bitrate |
|----------------|----------|---------|
| 0.8 µs | 13.6 µs | **23.06 Gbps** ← maximum |
| 1.6 µs | 14.4 µs | 21.78 Gbps |
| 3.2 µs | 16.0 µs | 19.60 Gbps |

---

# Exercise 4: Wi-Fi Network Design

## (a) Application Traffic Characteristics

**Video stream:**
- Application data per frame: 7,300 bytes + 12 bytes RTP header = 7,312 bytes
- UDP payload per IP packet: 1,500 − 20 (IP) − 8 (UDP) = 1,472 bytes
- IP packets per video frame: ⌈7,312 / 1,472⌉ = **5 IP packets**
  - 4 × full: 20 + 8 + 1,472 = **1,500 bytes**
  - 1 × last: 20 + 8 + (7,312 − 4 × 1,472) = 20 + 8 + 1,424 = **1,452 bytes**

**Monitoring:**
- IP packet: 72 (data) + 8 (UDP) + 20 (IP) = **100 bytes** → fits in one IP packet

| Application | IP Packet Size | DL (AP→STA) / UL (STA→AP) | Traffic behavior |
|-------------|---------------|---------------------------|-----------------|
| Video | 1,500 bytes (max), 5 packets/frame | **DL** (AP → STA) | CBR, periodic at 50 fps |
| Monitoring | 100 bytes | **UL** (STA → AP) | Periodic, 1 msg / 100 ms per STA |

---

## (b) T_Data Expression

For one PPDU carrying N aggregated packets of size P bytes at PHY rate R, using HT Greenfield format.

**HT Greenfield preamble structure:**

```
| HT-GF-STF | HT-LTF 1 | HT-SIG | HT-LTF 2 … N_DLTF |  DATA  |
|   8 µs    |   8 µs   |  8 µs  |  (N_DLTF−1) × 4 µs |        |
```

The number of data LTFs N_DLTF = smallest value in {1, 2, 4} ≥ N_SS:

| N_SS | N_DLTF | Extra LTFs after SIG | Extra time |
|------|--------|----------------------|------------|
| 1 | 1 | 0 | 0 µs |
| 2 | 2 | 1 | 4 µs |
| 3 or 4 | 4 | 3 | 12 µs |

**This exercise specifies "No spatial multiplexing" → N_SS = 1 → N_DLTF = 1 → no extra HT-LTFs after HT-SIG.**

Total preamble = 8 + 8 + 8 + 0 = **24 µs**

```
T_Data = 24 µs + (N × P × 8) / R
```

where P includes the full MPDU payload (IP + LLC + MAC header + FCS).

---

## (c) Network Design

### Step 1 — Channel Band

**Choose 5 GHz.**

Justification: 5 GHz has less interference than 2.4 GHz (fewer overlapping networks, less congestion from Bluetooth/microwave). Minimum MCS 3 (16-QAM, 1/2 coding) is guaranteed for all STAs.

### Step 2 — Bandwidth

**Choose 40 MHz.**

From the MCS table, 5 GHz, MCS 3, Short GI, 40 MHz: R = **60 Mbps**.

This gives sufficient capacity with room for multiple streams and measurement STAs.

### Step 3 — Timing verification (using T_Data formula)

**Video DL** — A-MPDU with 5 MPDUs per video frame:

```
P_MPDU = LLC(8) + IP(1500) + MAC_header(24) + FCS(4) = 1,536 bytes
Total = 5 × 1,536 = 7,680 bytes

T_Data = 24 µs + 7,680 × 8 / (60 × 10⁶)
       = 24 + 1,024
       = 1,048 µs ≈ 1.05 ms   ≪ 200 ms delay budget ✓
```

**Monitoring UL** — one IP packet per poll, PCF polling:

```
P_MPDU = LLC(8) + IP(100) + MAC_header(24) + FCS(4) = 136 bytes

T_Data = 24 + 136 × 8 / (60 × 10⁶) = 24 + 18.1 = 42.1 µs

Per STA: T_POLL + SIFS + T_Data + SIFS = 40 + 16 + 42.1 + 16 = 114.1 µs
200 STAs: 200 × 114.1 = 22,820 µs = 22.8 ms   < 100 ms delay budget ✓
```

### Design Summary

| Config | Choice | Justification |
|--------|--------|---------------|
| Channel | **5 GHz** | Less interference; MCS 3 guaranteed; suitable for high-rate short-range links |
| Bandwidth | **40 MHz** | 60 Mbps at MCS 3; handles video + 200 monitoring STAs within delay budget |
| MAC mechanism | **DCF (CSMA/CA + RTS/CTS)** for video DL; **PCF** for monitoring UL | PCF polling ensures bounded UL delay for 200 STAs; RTS/CTS avoids hidden terminal for large video frames |
| Aggregation | **A-MPDU, 5 MPDUs** per video PPDU | Amortises 24 µs preamble over 5 packets; No ACK policy eliminates BlockACK overhead |

---

## (d) Saturation Check

**Medium time consumed per second:**

```
Video DL (with DIFS + backoff):
  DIFS = 16 + 2×9 = 34 µs; avg backoff = (15/2)×9 = 67.5 µs
  Per PPDU: 34 + 67.5 + 1,048 = 1,149.5 µs
  50 frames/s: 50 × 1,149.5 = 57,475 µs = 57.5 ms/s

Monitoring UL (PCF, no contention overhead):
  200 STAs polled every 100 ms → 10 rounds/s
  Per round: 200 × 114.1 µs = 22,820 µs = 22.8 ms
  Per second: 10 × 22.8 = 228 ms/s
```

**Total used:** 57.5 + 228 = **285.5 ms/s**

**Remaining capacity:** 1,000 − 285.5 = **714.5 ms/s** (≈ 71.5 % free)

**The network is NOT saturated.**

Additional capacity available:
- Each extra video stream uses ≈ 57.5 ms/s → room for **≈ 12 more streams**
- Each extra monitoring STA uses ≈ 10 × 114.1 µs = 1.14 ms/s → room for **hundreds more STAs**

---

# Exercise 5: Cellular Networks — GSM & Spectrum Regulation

## (a) Simultaneous Full-Rate Calls in One GSM Cell

**Total physical channels:**

```
16 frequencies × 8 time slots/frequency = 128 physical channels
```

**Channels reserved for broadcast and common control:**

```
5 time slots reserved for BCCH, FCCH, SCH, CCCH (PAGCH)
```

**Channels needed for SDCCH** (call setup signaling — 50 simultaneous setups required):

Each SDCCH/8 sub-channel uses 1 physical time slot and supports 8 parallel setups.

```
Number of SDCCH/8 slots = ⌈50/8⌉ = 7 slots  (7 × 8 = 56 ≥ 50)
```

**TCH channels available for active voice calls:**

```
TCH = 128 − 5 (broadcast/control) − 7 (SDCCH)
    = 116 simultaneous full-rate calls
```

---

## (b) Cellular vs. Wi-Fi — Spectrum Regulation & Radio Resource Management

| | **Cellular (4G/5G)** | **Wi-Fi** |
|---|---|---|
| **Spectrum regulation** | Licensed, exclusive spectrum — operators purchase frequency bands at government auctions. Each operator owns their band; no one else can transmit in it. | Unlicensed, shared spectrum (ISM/UNII bands). Anyone can transmit without a licence, subject to power limits and etiquette rules (e.g., ETSI EN 300 328). |
| **Radio resource management** | Centralized: the base station (eNodeB/gNB) schedules every uplink and downlink resource block per slot. Guarantees QoS per user; inter-cell interference is managed by the network via frequency planning, power control, and coordination (X2 interface). | Distributed: CSMA/CA contention-based — each device decides independently when to transmit. No QoS guarantee (EDCA gives statistical priority only). Interference from neighboring APs/STAs is uncontrolled; adjacent deployments cannot coordinate. |

**Consequences:**
- Cellular operators can guarantee service quality and plan coverage/capacity, but spectrum licensing is expensive → cost passed on to users via subscriptions.
- Wi-Fi is free to deploy but QoS degrades in dense environments where many devices/APs compete for the same unlicensed channel.

---

## (c) "Cellular and Wi-Fi standards are converging"

Several dimensions of convergence:

1. **Physical layer convergence:** Both now use OFDM/OFDMA modulation, MIMO, and beamforming. Wi-Fi 6 (802.11ax) borrowed OFDMA directly from LTE/5G; 5G NR adopted flexible numerology inspired by Wi-Fi's OFDM design.

2. **Feature convergence:** Cellular brought carrier aggregation and QoS bearers; Wi-Fi responded with EDCA (QoS), Block ACK, MU-MIMO (802.11ac/ax). 5G NR-U (New Radio Unlicensed) runs 5G protocols in Wi-Fi's unlicensed 5 GHz / 6 GHz bands.

3. **Spectrum convergence:** 5G is entering unlicensed bands (Wi-Fi territory), while private LTE/5G networks use spectrum that was once reserved for cellular operators only. The 6 GHz band is now shared between Wi-Fi 6E and private 5G deployments.

4. **Architecture convergence:** 3GPP defines tight WLAN–cellular interworking (EPC-integrated Wi-Fi offload, Passpoint/Hotspot 2.0, VoWiFi). Operators seamlessly hand traffic between cellular and Wi-Fi without the user noticing.

5. **Use case convergence:** Wi-Fi is used for voice calls (VoWiFi), video conferencing, and IoT — use cases once considered exclusively cellular. Cellular (NB-IoT, LTE-M) now targets low-power sensor deployments that were once only served by Zigbee/Wi-Fi.

In short: the boundary between "cellular" and "Wi-Fi" is blurring at the PHY, MAC, spectrum, and architecture levels.

---

# Exercise 6: AODV in a Wireless Ad Hoc Network

## Network Topology

```
        1 ── 2 ── 3
        │    │    │
    4 ── 5 ── 6 ── 7 ── 8
        │    │    │
        9 ── 10── 11
```

Node 6 is the server. All 10 other nodes want to connect to Node 6.

**Hop distances from Node 6:**

| Source node | Shortest path to 6 | Hops (h) |
|-------------|-------------------|----------|
| 1 | 6→5→1 | 2 |
| 2 | 6→2 | 1 |
| 3 | 6→7→3 | 2 |
| 4 | 6→5→4 | 2 |
| 5 | 6→5 | 1 |
| 7 | 6→7 | 1 |
| 8 | 6→7→8 | 2 |
| 9 | 6→5→9 | 2 |
| 10 | 6→10 | 1 |
| 11 | 6→7→11 | 2 |

---

## (a) Worst-Case Total Routing Control Overhead

In AODV, for each route discovery:
- **RREQs:** (N − 1) = 10 broadcasts (every node except destination rebroadcasts once)
- **RREPs:** h unicast forwards back to the source (one per hop on the return path)

With **10 sources** connecting simultaneously (worst case = all at once, no caching):

```
Total RREQs = 10 sources × 10 RREQs each = 100 RREQ transmissions

Total RREPs = Σ h_i = 2+1+2+2+1+1+2+2+1+2 = 16 RREP transmissions

Total control overhead = 100 + 16 = 116 control packets
```

---

## (b) Why Control Overhead Can Be Lower in Reality

1. **Route caching / intermediate RREP:** If a node already has a valid, unexpired routing table entry for Node 6 (learned from a previous RREQ flood), it can reply with an RREP immediately without forwarding the RREQ further. This cuts the RREQ flood short.

2. **Simultaneous setup reuse:** When 10 RREQs flood the network simultaneously, intermediate nodes that forward RREQ_A (from node 1) also record a reverse route back to node 1. Node 6 can then piggyback replies, and intermediate nodes gain routing knowledge for free.

3. **AODV route entry lifetime:** After the first connection is established, routing table entries remain valid for a TTL period. Subsequent connections from the same source reuse the cached route without re-flooding.

4. **Expanding ring search (TTL limiting):** If the destination is nearby, the RREQ is stopped before reaching all N nodes, dramatically reducing RREQs for short paths.

---

## (c) Method Wi-Fi Uses to Avoid RREQ Collision

Wi-Fi uses **random jitter** before rebroadcasting: when a node receives a RREQ and decides to forward it, it waits a **random backoff delay** before actually transmitting. This desynchronises the rebroadcasts from multiple neighbours, reducing the probability that two nodes transmit simultaneously and collide.

This is the standard IEEE 802.11 MAC collision-avoidance mechanism applied to broadcast RREQ propagation — a mitigation for the **broadcast storm problem**.

---

## (d) Effect of Using Average Connection Speed as Route Metric

Standard AODV selects the route whose RREQ **arrives first** (minimum delay metric). Switching to **average connection speed** changes the procedure as follows:

1. **RREQ carries accumulated metric:** Each intermediate node appends the speed of the incoming link to the RREQ. The metric propagated is the minimum (bottleneck) link speed along the path so far.

2. **Destination cannot reply immediately:** Node 6 must wait to receive RREQs from multiple paths before it knows which path has the best metric. It sets a timer and waits for additional RREQs before sending the RREP.

3. **Multiple RREPs may be sent:** As better-metric RREQs arrive, Node 6 may send a new RREP superseding an earlier one. Intermediate nodes update their routing tables if the new RREP carries a better metric.

4. **Higher setup latency:** The connection setup is delayed because the destination waits for the best-metric RREQ rather than responding to the first one.

5. **Better path quality:** The selected route maximises bottleneck link speed, improving throughput at the cost of longer discovery time and potentially more control traffic.
