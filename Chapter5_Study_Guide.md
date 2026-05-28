# MOBAN Chapter 5 — Comprehensive Study Guide
# Wireless LAN (IEEE 802.11 / Wi-Fi)

---

## PART 1: THEORY SUMMARY

---

### 1. Design Goals & Characteristics

**Design goals of WLANs:**
- Global operation (available worldwide)
- License-free operation (ISM bands)
- Robust transmission technology
- Easy to use, spontaneous cooperation
- Low power for battery operation
- Interoperability with existing wired networks
- Transparency for applications
- Security, privacy and safety

**Advantages:**
- Very flexible within radio coverage
- Ad Hoc networks possible without prior planning
- Invisible design
- More robust against disasters
- No additional cost when adding users
- Long equipment lifetime

**Disadvantages:**
- Lower bandwidth, lower QoS, higher delay/jitter vs. wired (shared medium)
- Lower safety, security, privacy
- Many proprietary solutions; standards take time
- National restrictions on radio operation

---

### 2. IEEE 802.11 / Wi-Fi Introduction

**Wi-Fi** = registered trademark of the Wi-Fi Alliance; tagline "Wireless Fidelity"
- Wi-Fi Certified™ = interoperability label among vendors
- Based on IEEE 802.11 family of standards

**IEEE 802.11:**
- Specifies **PHY** and **MAC** layers
- Same interface to higher layers (LLC) — interoperable with 802.x family
- First version: IEEE Std 802.11-1997; Latest: IEEE Std 802.11-2024

**Protocol stack position:**
```
Application → TCP → IP → LLC → [802.11 MAC | 802.11 PHY]
```

---

### 3. Architecture

#### 3.1 Infrastructure vs. Ad Hoc

| Feature | Infrastructure | Ad Hoc |
|---------|---------------|--------|
| Control | Central (AP) | Distributed |
| Client complexity | Simple | More complex |
| Hop count | Single-hop to AP | Multi-hop possible |

#### 3.2 Key Terms

| Term | Definition |
|------|-----------|
| **STA** (Station) | Terminal with radio contact and medium access |
| **AP** (Access Point) | Station integrated into WLAN and the Distribution System |
| **BSS** (Basic Service Set) | AP + stations within same radio coverage |
| **ESS** (Extended Service Set) | Multiple BSSs connected via DS = one logical network |
| **DS** (Distribution System) | Wired interconnection network between APs |
| **Portal** | Bridge to other (wired) networks |
| **IBSS** (Independent BSS) | Ad hoc group of STAs in same coverage, same frequency |

#### 3.3 Protocol Architecture Layers

```
PMD  (Physical Medium Dependent)    — modulation, coding
PLCP (Physical Layer Convergence Protocol) — CCA, SAP
MAC  (Medium Access Control)        — access, fragmentation, encryption
LLC  (Logical Link Control)         — interface to higher layers
MAC Management                      — association, roaming, auth, power mgmt
PHY Management                      — channel selection, PHY MIB
Station Management                  — coordinates all management functions
```

---

### 4. Physical Layer (PHY)

#### 4.1 PHY Summary Table

| Standard | PHY Type | Frequency | Data Rates | Year |
|----------|---------|-----------|-----------|------|
| 802.11-1997 | FHSS | 2.4 GHz | 1, 2 Mbps | 1997 |
| 802.11-1997 | DSSS | 2.4 GHz | 1, 2 Mbps | 1997 |
| 802.11b | HR-DSSS | 2.4 GHz | 1, 2, 5.5, **11** Mbps | 1999 |
| 802.11a | OFDM | 5 GHz | 6–**54** Mbps | 1999 |
| 802.11g | ERP (OFDM) | 2.4 GHz | 1–**54** Mbps | 2003 |
| 802.11n | HT PHY (MIMO) | 2.4 & 5 GHz | 6.5–**600** Mbps | 2009 |

#### 4.2 IEEE 802.11-1997: Basic Standard

**Three original PHYs:**
- FHSS (Frequency Hopping Spread Spectrum) — 2.4 GHz
- DSSS (Direct Sequence Spread Spectrum) — 2.4 GHz
- Infrared (IR)

**CCA (Clear Channel Assessment):** indicates if the medium is idle (carrier sense)

**Power limits:**
- USA: 1 W max transmit power
- EU: 100 mW EIRP, min 1 mW
- Japan: 10 mW/MHz

**FHSS PHY:**
- 79 channels of 1 MHz each (US/Europe)
- Minimum 2.5 hops/s
- Modulation: 2-level GFSK (1 Mbps) or 4-level GFSK (2 Mbps)

**DSSS PHY:**
- Barker code chipping sequence: `+1, -1, +1, +1, -1, +1, +1, +1, -1, -1, -1`
- 11 Mchips/s
- Modulation: DBPSK (1 Mbps), DQPSK (2 Mbps)

#### 4.3 CRC (Cyclic Redundancy Check)

**Purpose:** Detect transmission errors.

**Concept:**
- Sender: multiply data D by 2ⁿ, divide by generator polynomial, append remainder
- Receiver: divide received frame by same polynomial → remainder = 0 means no error

**Example (n=3, polynomial x³+x+1 → pattern 1011):**
```
Data D = 111001010 (9 bits)
Transmitted = 111001010 | 010  (frame = D·2³ + remainder)
Receiver divides by 1011 → remainder = 0 → OK
```

**Key formulas:**
- Frame size: k + n bits (k = data, n = redundancy bits)
- Polynomial of order n → pattern of n+1 bits

#### 4.4 Scrambling

**Purpose:** DC blocking, spectral whitening, self-clocking.

**Concept:**
- Sender: shift data back n bits, divide by polynomial → quotient = scrambled data
- Receiver: multiply scrambled data by polynomial, shift forward n bits → original data

**Note:** Unlike CRC (where remainder is transmitted), with scrambling the **quotient** is transmitted — there is no remainder appended.

**DSSS PHY scrambler polynomial:** s(z) = z⁷ + z⁴ + 1

#### 4.5 FHSS PHY Frame (PPDU)

```
| Sync (80b) | SFD (16b) | PLW (12b) | PSF (4b) | HEC (16b) | PSDU (var) |
|-- PLCP Preamble (1Mbps) --|--- PLCP Header (1Mbps) ---|-- payload --|
```
- **Sync:** 01010101... for bit sync and CCA
- **SFD:** frame sync pattern `0000110010111101`
- **PLW:** payload length in bytes (<4096); includes 32-bit CRC of payload
- **PSF:** data rate (0000 = 1 Mbps, 0010 = 2 Mbps)
- **HEC:** 16-bit CRC of PLCP header using polynomial x¹⁶+x¹²+x⁵+1

#### 4.6 DSSS PHY Frame (PPDU)

```
| Sync (128b) | SFD (16b) | Signal (8b) | Service (8b) | Length (16b) | HEC (16b) | PSDU (var) |
|--- PLCP Preamble (1Mbps) ---|---------  PLCP Header (1Mbps) -------------|-- payload --|
```
- **Sync:** scrambled 1-bits for sync, energy detection, frequency offset compensation
- **SFD:** `1111001110100000`
- **Signal:** data rate (0x0A = 1 Mbps DBPSK; 0x14 = 2 Mbps DQPSK)
- **Length:** payload length in **microseconds** (<65536 µs)
- All PHY bits are scrambled with s(z) = z⁷+z⁴+1

#### 4.7 IEEE 802.11b: HR-DSSS (High Rate DSSS)

**Key change:** CCK (Complementary Code Keying) for 5.5 and 11 Mbps

**Rates:** 1 Mbps (DBPSK), 2 Mbps (DQPSK), 5.5 Mbps (CCK), 11 Mbps (CCK)

**Channel plan (2.4 GHz ISM band):**
- 11–14 channels total (geography-dependent)
- Channel spacing: 5 MHz
- Channel bandwidth: 22 MHz
- **3 non-overlapping channels:**
  - Europe: CH 1, 7, 13
  - US/Canada: CH 1, 6, 11

**Range:** 300 m outdoor, 30 m indoor (max rate ~10 m indoor)

**HR-DSSS frame:** Same structure as DSSS but with long preamble of 192 µs at 1 Mbps DBPSK; SFD is mirrored.

#### 4.8 IEEE 802.11a: OFDM at 5 GHz

**OFDM parameters:**
- 64 subcarriers total; 52 used (48 data + 4 pilot)
- 12 virtual (guard) subcarriers
- Subcarrier spacing: 312.5 kHz
- Occupied bandwidth: 16.6 MHz (20 MHz channel)
- Symbol duration: 4 µs = 3.2 µs data + 0.8 µs guard interval
- Symbol rate: 250,000 OFDM symbols/s

**Data rates and modulation:**

| Rate | Modulation | Code Rate |
|------|-----------|-----------|
| 6, 9 Mbps | BPSK | 1/2, 3/4 |
| 12, 18 Mbps | QPSK | 1/2, 3/4 |
| 24, 36 Mbps | 16-QAM | 1/2, 3/4 |
| 48, 54 Mbps | 64-QAM | 2/3, 3/4 |

Mandatory: 6, 12, 24 Mbps

**Frequency (Europe):** 5.15–5.35 GHz and 5.725–5.825 GHz  
**Channel spacing:** 20 MHz  
**Center frequency:** 5000 + 5 × channel_number [MHz]

**802.11a PHY frame:**
- Preamble: 16 µs (10 short + 2 long symbols) — for sync, channel estimation
- Signal field: 1 OFDM symbol at 6 Mbps — carries rate, reserved, length (bytes), parity, tail
- Data field: variable, at chosen rate

**Range examples at 5 GHz:** 54 Mbps ≤5 m, 36 Mbps ≤25 m, 18 Mbps ≤40 m, 12 Mbps ≤60 m

#### 4.9 IEEE 802.11g: ERP (Extended Rate PHY) at 2.4 GHz

- Combines HR-DSSS (for 1, 2, 5.5, 11 Mbps) with OFDM (for 6–54 Mbps) in 2.4 GHz
- Same frame formats: Long/Short PPDU (from 802.11b) and ERP-OFDM PPDU (from 802.11a)
- Greater range than 802.11a due to lower frequency (300 m outdoor, 30 m indoor)
- Heavy interference on 2.4 GHz ISM band

#### 4.10 IEEE 802.11n: HT PHY (High Throughput)

**How 802.11n reaches 600 Mbps — step by step:**

| Enhancement | Change | Rate impact |
|------------|--------|------------|
| More subcarriers | 48 → 52 data subcarriers | 54 → 58.5 Mbps |
| Better FEC | coding rate 3/4 → 5/6 | 58.5 → 65 Mbps |
| Short Guard Interval | 800 ns → 400 ns (4µs → 3.6µs symbol) | 65 → 72.2 Mbps |
| MIMO (4×4) | SISO → 4 spatial streams | 72.2 → 288.9 Mbps |
| 40 MHz channels | Bond two 20 MHz → 108 subcarriers | 288.9 → **600 Mbps** |

**Frequency:** 2.4 GHz AND 5 GHz  
**Range:** 70–200 m indoor, 200–800 m outdoor

**PHY frame formats:**
- **Non-HT PPDU:** Same as 802.11a/g — backward compatible
- **HT-Greenfield PPDU:** Optimized for 802.11n only; NOT backward compatible (causes collisions with 802.11a/g neighbors)
- **HT-Mixed mode PPDU:** Combines non-HT preamble + new HT preamble; 802.11a/g clients can associate AND neighbors can decode preamble and back off — recommended for deployments with mixed clients

**MCS (Modulation and Coding Scheme):** single index encoding modulation + coding rate + stream count. Fast MCS feedback enables per-packet link adaptation.

---

### 5. MAC Layer

#### 5.1 Traffic Services

| Service | Type | How |
|---------|------|-----|
| Asynchronous Data | Mandatory | Best-effort, broadcast/multicast supported |
| Time-Bounded | Optional | PCF (Point Coordination Function) polling |

#### 5.2 Medium Access Methods

**DFWMAC = Distributed Foundation Wireless MAC**

Three access methods:

1. **DFWMAC-DCF CSMA/CA** (mandatory) — asynchronous, collision avoidance via random back-off + ACK
2. **DFWMAC-DCF with RTS/CTS** (optional) — solves hidden/exposed terminal; RTS reserves medium via NAV
3. **DFWMAC-PCF** (optional, infrastructure only) — AP polls STAs; supports time-bounded service

#### 5.3 Inter-Frame Spaces (IFS) — Priority Mechanism

```
SIFS < PIFS < DIFS         (shorter IFS = higher priority)
```

| IFS | Priority | Usage | Formula |
|-----|---------|-------|---------|
| **SIFS** | Highest | ACK, CTS, polling response | PHY-specific |
| **PIFS** | Medium | PCF time-bounded service | SIFS + 1 slot |
| **DIFS** | Lowest | Asynchronous data (DCF) | SIFS + 2 slots |

**802.11a parameters:** T_SIFS = 16 µs, T_slot = 9 µs → DIFS = 16 + 2×9 = 34 µs

#### 5.4 DFWMAC-DCF CSMA/CA

**Procedure:**
1. Station senses medium (CCA)
2. If medium is **free for DIFS**: transmit immediately
3. If medium is **busy** (or after any successful transmission): wait DIFS + random back-off
4. Back-off timer **freezes** if another station occupies the medium (fairness)
5. Resume countdown when medium is free again; transmit when timer reaches 0

**Contention Window (CW) — Exponential Back-off:**
- Low CW → many collisions; High CW → high delay
- On collision: CW doubles
- 802.11n example: CWmin = 15, CWmax = 1023
  → CW sequence: 15, 31, 63, 127, 255, 511, 1023
- Back-off = random value in [0, CW], unit = slot time

**Unicast frames:**
- Sender waits DIFS (+ back-off if medium busy), then sends
- Receiver replies after **SIFS** with ACK if CRC OK
- No ACK → retransmit with new random back-off

**Broadcast/multicast:** No ACK

#### 5.5 DFWMAC-DCF with RTS/CTS

**Procedure:**
1. Sender waits DIFS, sends **RTS** (with duration field = time needed for data + ACK + SIFSes)
2. Receiver replies after SIFS with **CTS**
3. Sender transmits data after SIFS
4. Receiver acknowledges with ACK after SIFS
5. All other stations hearing RTS or CTS set their **NAV** (Network Allocation Vector) → virtual carrier sense → defer access

**Solves:**
- **Hidden terminal problem:** Station hidden from sender hears CTS → defers
- **Exposed terminal problem:** Station exposed to sender hears RTS/CTS → can transmit to other destination

**Duration field values:**
- In ACK: 0 (no more fragments) or remaining duration minus ACK time − SIFS
- In RTS: time for CTS + data + ACK + 3×SIFS
- In CTS: duration from RTS − time for CTS − SIFS

**Fragmentation:**
- MAC-layer frame splitting to adapt to error rate (transparent to user)
- Each fragment (except last) reserves medium for next fragment via duration field
- Frame: RTS → CTS → frag1 → ACK1 → frag2 → ACK2 (with SIFS between each)

#### 5.6 DFWMAC-PCF with Polling

**Structure:** Superframe = Contention-Free Period (CFP) + Contention Period (CP)

- AP = Point Coordinator; polls STAs in a list
- CFP starts with PIFS (higher priority than DIFS → AP wins contention)
- STAs set NAV to cover entire CFP
- If PCF only (no CP): equivalent to TDMA with TDD
  - Extra overhead when nodes have nothing to send

**Limits of PCF:** No QoS guarantees, inflexible superframe length.

#### 5.7 MAC Frame Format

```
| Frame Control (2B) | Duration/ID (2B) | Addr1 (6B) | Addr2 (6B) | Addr3 (6B) | Seq Control (2B) | Addr4 (0|6B) | MSDU (0-2312B) | CRC (4B) |
```

**Duration/ID:** Medium occupation in µs for NAV (<32768)  
**Sequence Control:** Filters duplicate frames (due to lost ACKs)  
**CRC:** 32-bit checksum (FCS)

**Frame Control subfields:**

| Field | Bits | Meaning |
|-------|------|---------|
| Protocol version | 2 | 0 = current protocol |
| Type | 2 | 00=management, 01=control, 10=data, 11=reserved |
| Subtype | 4 | E.g., beacon (1000), RTS (1011), CTS (1100), data (0000) |
| To DS / From DS | 1+1 | Direction relative to Distribution System |
| More Frag | 1 | Another fragment follows |
| Retry | 1 | Retransmission frame |
| Power Mgmt | 1 | 1 = power-save mode |
| More Data | 1 | AP has more data for sleeping STA |
| Protected Frame | 1 | Cryptographic encapsulation applied |
| Order | 1 | Process frames strictly in order |

#### 5.8 MAC Address Format

**Four address fields:**
- **Address 1:** Physical receiver (filter here; ACK sent back to Address 2)
- **Address 2:** Physical transmitter
- **Address 3:** Logical receiver/sender/BSSID
- **Address 4:** Logical sender (used only in DS-to-DS)

| Scenario | To DS | From DS | Addr1 | Addr2 | Addr3 | Addr4 |
|----------|-------|---------|-------|-------|-------|-------|
| Ad hoc | 0 | 0 | DA=RA | SA=TA | IBSSID | — |
| Infra: from AP | 0 | 1 | DA=RA | BSSID=TA | SA | — |
| Infra: to AP | 1 | 0 | BSSID=RA | SA=TA | DA | — |
| Infra: within DS | 1 | 1 | RA | TA | DA | SA |

---

### 6. MAC Layer — IEEE 802.11e (QoS)

#### 6.1 Why DCF/PCF is Not Enough

**DCF limitations:** No traffic differentiation, packet fairness (all stations same priority), best-effort only.  
**PCF limitations:** Fixed superframe → inflexible, no mechanism to specify traffic requirements.

#### 6.2 HCF: Hybrid Coordination Function (802.11e-2005)

Two new access methods:
- **HCCA** (Hybrid Coordination Channel Access): PCF-like but more flexible CFP initiation
- **EDCA** (Enhanced Distributed Channel Access): DCF-like but with priority differentiation

#### 6.3 EDCA Access Categories

4 Access Categories (AC) mapped from 8 IEEE 802.1D User Priorities (UP):

| AC | Traffic Type | Priority |
|----|-------------|---------|
| AC_VO | Voice | Highest |
| AC_VI | Video | High |
| AC_BE | Best Effort | Low |
| AC_BK | Background | Lowest |

#### 6.4 EDCA Prioritization Mechanisms

**1. AIFS (Arbitration Inter-Frame Space):**
```
AIFS[AC] = SIFS + AIFSN[AC] × T_slot
```
- Higher priority AC → smaller AIFSN[AC] → smaller AIFS → wins medium sooner
- Compare: DIFS = SIFS + 2 × T_slot (DCF)

**2. Contention Window per AC:**
- CWmin[AC] and CWmax[AC] differ per AC
- Higher priority → smaller CWmin, CWmax → smaller average backoff

**3. TXOP (Transmission Opportunity):**
- Once a station wins the medium for an AC, it can send **multiple frames continuously** for a set duration
- Only SIFS between successive frames within a TXOP

**Intra-station contention:** Each station runs 4 independent back-off engines (one per AC); internal winner competes on the wireless medium.

#### 6.5 EDCA Limitations

- Still based on statistical fairness — no hard guarantees
- Streams of same priority still compete
- Performance varies with load and device implementation
- **Admission Control** needed for hard guarantees

#### 6.6 Block Acknowledgment (Block ACK)

Introduced in 802.11e, critical for 802.11n:

- Sender sets ACK Policy = "Block ACK" in QoS Control field
- Multiple frames sent in a TXOP with only SIFS between them (no individual ACKs)
- Sender sends **BlockAckReq** after TXOP
- Receiver sends single **BlockAck** with bitmap indicating status of up to 64 MSDUs

**Immediate vs. Delayed Block ACK:**
- Immediate: BlockAck sent after SIFS
- Delayed: BlockAck sent after DIFS + back-off (for complex processing)

**802.11e MAC frame addition:** QoS Control field (2 bytes) appended when QoS subfield in Subtype = 1.

---

### 7. MAC Layer — IEEE 802.11n (Frame Aggregation)

**Goal:** Reduce per-frame overhead (preamble, DIFS, back-off, ACK) by sending more data per transmission cycle.

**Two aggregation types:**

| | A-MSDU | A-MPDU |
|--|--------|--------|
| What is aggregated | Multiple MSDUs into one MPDU | Multiple MPDUs in one PPDU |
| Max frame size | 7935 bytes (vs. 2304 standard) | 64535 bytes (vs. 4095) |
| ACK | Single ACK | Block ACK (no prior BlockAckReq needed) |
| Error handling | Lose all on error | Only retransmit failed MPDUs |

---

### 8. MAC Management

#### 8.1 Synchronization

**Infrastructure:** AP periodically broadcasts **beacon frames** containing:
- Timestamp (real time!) — stations adjust local clock
- Purpose: power management, PCF super-frame prediction, FHSS sequence sync

**Ad hoc:** Each node sends beacons; back-off applies to beacons; collisions possible → beacon lost.

**Beacon interval:** Typically 100 ms; delayed if medium busy (AP waits for idle medium).

#### 8.2 Power Management

**Goal:** Switch off transceiver when not needed.

**States:** Sleep ↔ Awake

**TSF (Timing Synchronization Function):** Ensures all stations wake up at the same time.

**Infrastructure mode (AP manages):**
- **TIM (Traffic Indication Map):** Beacon field listing STAs with buffered unicast frames
- **DTIM (Delivery TIM):** Announces buffered broadcast/multicast frames
- STA wakes at TIM interval, checks TIM, retrieves data if indicated, sleeps again
- Trade-off: longer TIM interval → more power saving, but higher delay

**Ad hoc mode:**
- **ATIM (Ad hoc TIM):** Stations announce buffered frames to receivers
- More complex: no central AP, all stations buffer, collision of ATIMs possible

#### 8.3 Roaming (Re-association)

**Trigger:** No or bad connection (RSSI below threshold)

**Steps:**
1. **Scanning:** Passive (listen for beacons) or Active (send Probe Request, receive Probe Response)
2. **Re-association Request:** STA sends to new AP
3. **Re-association Response:** AP accepts → STA joins
4. AP informs DS → DS updates location database → old AP releases resources

**Note:** No universal roaming solution across all vendors today.

---

### 9. Further Developments (802.11ax / Wi-Fi 6)

#### 9.1 Paradigm Shift: Efficiency over Speed

Earlier 802.11 revisions focused on raw speed (higher PHY rate). **802.11ax** (Wi-Fi 6) targets **high efficiency** in dense deployments.

**Four pillars of Wi-Fi 6:**
1. UL/DL OFDMA
2. UL/DL MU-MIMO
3. BSS Coloring
4. Other enhancements (1024-QAM, TWT, etc.)

#### 9.2 OFDMA (Orthogonal Frequency Division Multiple Access)

**Concept:** Partition the frequency-time space into Resource Units (RUs) and assign different RUs to different clients simultaneously.

**802.11ac:** One client uses the full channel per TXOP (contention per client).  
**802.11ax:** AP coordinates multiple clients in one TXOP using sub-channel RUs.

**RU sizes in 20 MHz:** 26, 52, 106, 242 subcarriers  
**In 80 MHz:** up to 996-subcarrier RU

**Benefits:**
- Lower latency for short frames (don't wait for full channel)
- Both uplink AND downlink
- Single contention for multiple clients → reduces overhead

#### 9.3 MU-MIMO (Multi-User MIMO)

**Concept:** AP uses spatial diversity to transmit to/from multiple clients simultaneously.

| | 802.11ac | 802.11ax |
|--|---------|---------|
| Downlink MU-MIMO | Yes (up to 4 streams) | Yes (up to 8×8) |
| Uplink MU-MIMO | No | Yes |
| Max streams | 4 clients | 8 streams (Wave 2) |

**MU-MIMO + OFDMA:** Combined in 802.11ax for RUs ≥ 106 subcarriers.

#### 9.4 BSS Coloring

**Problem:** Overlapping BSSs (OBSS) → stations far from each other back off unnecessarily, wasting airtime.

**Traditional CCA:** If signal > SD threshold → backoff (regardless of which BSS it belongs to).

**Solution — BSS Color:**
- 6-bit BSS identifier appended to PHY and MAC frames
- Two SD thresholds:
  - **Same color:** Standard threshold — backoff (it's a neighbor in same BSS, collision possible)
  - **Different color:** Higher threshold — can transmit simultaneously (spatial reuse)

**Result:** Dense deployments with multiple APs on same channel can transmit simultaneously → less contention overhead.

#### 9.5 Wi-Fi 6E

- Extends 802.11ax into the **6 GHz band** (5945–6425 MHz in Europe)
- Adds approximately 480 MHz of new spectrum
- Less congested than 2.4 GHz and 5 GHz

---

### 10. WLAN Throughput Calculation

#### 10.1 Fundamental Formula

```
Throughput = Useful data bytes / Cycle duration
```

**Useful data = IP payload** (not LLC, MAC, or PHY headers)

#### 10.2 Frame Encapsulation Stack (from exercise slides)

```
Application data
  + App header
  + TCP/UDP header     → APP packet
  + IP header          → TCP/UDP packet
  + LLC header (8B)    → IP packet
  + MAC header + CRC   → LLC packet (MSDU) → MPDU
  + PHY header + tail + padding → MAC Frame (PSDU) → PPDU
```

#### 10.3 One Cycle: CSMA/CA (No RTS/CTS)

```
Cycle = DIFS + back-off + T_PHY_hdr + T_MAC_hdr + T_LLC_hdr + T_data + SIFS + T_PHY_hdr_ACK + T_ACK
```

Where:
- DIFS = SIFS + 2 × T_slot
- T_back-off = (CWmin/2) × T_slot (average: CWmin = 15 → avg back-off = 7.5 slots)
- T_xxx = (frame size in bits) / PHY data rate
- Preamble + signal field transmitted at 6 Mbps (base rate); payload at negotiated rate

#### 10.4 One Cycle: CSMA/CA with RTS/CTS

```
Cycle = DIFS + back-off + T_RTS + SIFS + T_CTS + SIFS + T_data + SIFS + T_ACK
```

**Effect of RTS/CTS:** Adds two extra frames (RTS + CTS) per cycle → lower throughput for small packets but reduces collision probability in multi-station environments.

#### 10.5 Fragmentation Impact

- Large payload fragmented into N fragments of size f_max
- Each fragment: RTS + CTS + data_fragment + ACK (+ SIFSes)
- Total cycle = N × fragment_cycle
- More overhead per bit → lower throughput compared to no fragmentation

#### 10.6 Key Numbers for 802.11a (from exercises)

| Parameter | Value |
|-----------|-------|
| T_SIFS | 16 µs |
| T_slot | 9 µs |
| T_DIFS | 34 µs |
| CWmin | 15 |
| Max PHY rate | 54 Mbps |
| RTS frame | 20 bytes |
| CTS/ACK frame | 14 bytes |
| MAC header | 34 bytes |
| LLC header | 8 bytes |
| PHY preamble+signal | 16 µs + 4 µs = 20 µs |

#### 10.7 Exercise Solutions Summary

| Exercise | Setup | Throughput |
|----------|-------|-----------|
| 1 | 802.11a, CSMA/CA, 1500 B IP | 30.8 Mbps |
| 1 | 802.11a, CSMA/CA, 500 B IP | 16.6 Mbps |
| 2 | 802.11a, CSMA/CA + RTS/CTS, 1500 B | 25.6 Mbps |
| 3 | 802.11a, CSMA/CA + RTS/CTS + frag ≤500 B | 16.7 Mbps |
| 4 | 802.11a, CSMA/CA + RTS/CTS, 36 Mbps rate | 20.4 Mbps |
| 5 | 802.11n, CSMA/CA, 2 spatial streams, 1500 B | 31.58 Mbps |
| 6 | 802.11n, CSMA/CA, 2 SS, A-MPDU | 64.29 Mbps |
| 6 | 802.11n, CSMA/CA, 2 SS, A-MSDU | 65.55 Mbps |

**Key observations:**
- RTS/CTS reduces throughput for a single sender (extra overhead), but becomes beneficial with hidden terminals
- Fragmentation reduces throughput (more overhead per byte of useful data)
- Frame aggregation (A-MPDU, A-MSDU) roughly doubles throughput by amortizing MAC overhead

---

## PART 2: KEY FORMULAS REFERENCE

| Formula | Expression |
|---------|-----------|
| DIFS | SIFS + 2 × T_slot |
| PIFS | SIFS + 1 × T_slot |
| AIFS[AC] | SIFS + AIFSN[AC] × T_slot |
| Average back-off | (CWmin / 2) × T_slot |
| CW after k collisions | min(2^(k+1) − 1, CWmax) |
| Throughput (basic) | IP_bytes / (DIFS + backoff + T_data + SIFS + T_ACK) |
| 802.11a symbol duration | 4 µs (3.2 µs + 0.8 µs GI) |
| 802.11n short GI | 3.6 µs (3.2 µs + 0.4 µs GI) |
| Throughput gain with aggregation N frames | ≈ N × single_frame_gain / (overhead + N × data) |

---

## PART 3: CONCEPT COMPARISONS

### DCF vs. PCF vs. EDCA

| Feature | DCF | PCF | EDCA |
|---------|-----|-----|------|
| Medium access | CSMA/CA | Polling by AP | Enhanced CSMA/CA |
| Priority | None | Fixed | Per traffic class |
| QoS guarantee | No | Soft | No (statistical) |
| Contention-free | No | Yes (CFP) | No |
| Ad hoc compatible | Yes | No | Yes |

### 802.11a vs. 802.11b vs. 802.11g

| | 802.11b | 802.11a | 802.11g |
|--|---------|---------|---------|
| Frequency | 2.4 GHz | 5 GHz | 2.4 GHz |
| Max rate | 11 Mbps | 54 Mbps | 54 Mbps |
| Range | 300 m outdoor | 100 m outdoor | 300 m outdoor |
| Modulation | CCK | OFDM | CCK + OFDM |
| Interference | High (ISM) | Lower (less used) | High (ISM) |
| Backward compat. | — | Not with 802.11b | With 802.11b |

### OFDMA vs. MU-MIMO (Wi-Fi 6)

| | OFDMA | MU-MIMO |
|--|-------|---------|
| Multiplexing dimension | Frequency | Space |
| Best for | Many small packets | Large packets, few users |
| Channel division | Sub-channels (RUs) | Full channel, multiple beams |
| UL support in 802.11ax | Yes | Yes |

---

## PART 4: PRACTICE QUESTIONS

**Conceptual:**

1. Explain the difference between infrastructure and ad hoc 802.11 networks. What are the architectural implications?

2. Why are there only 3 non-overlapping channels in the 2.4 GHz band? Calculate this from the channel bandwidth and spacing.

3. Explain why CSMA/CA uses "collision avoidance" rather than "collision detection". What makes CD impossible in wireless?

4. Describe the hidden terminal problem and explain precisely how RTS/CTS solves it (what each station does upon hearing each frame).

5. Why does 802.11n introduce HT-Mixed mode rather than simply using Greenfield everywhere?

6. Explain why EDCA still cannot guarantee QoS for a real-time video stream, even though voice is prioritized over background traffic.

7. What is the purpose of scrambling in the DSSS PHY? Why is it different from CRC?

8. In 802.11 power management, what is the difference between TIM and DTIM? Why do both exist?

9. Explain BSS coloring. Why does using a higher CCA threshold for different-color BSSs enable spatial reuse without causing collisions?

10. A station sets its NAV when hearing an RTS. When is the NAV released, and what prevents it from transmitting before then?

**Calculation exercises:**

11. For 802.11a with CSMA/CA and no RTS/CTS: calculate the throughput for 1500-byte IP packets. (Answer: ~30.8 Mbps)

12. Repeat for 500-byte IP packets and explain why throughput is lower. (Answer: ~16.6 Mbps — PHY overhead is the same but useful data is smaller)

13. For 802.11a with RTS/CTS and 1500-byte packets, calculate throughput and compare to ex. 11. Explain the overhead difference. (Answer: ~25.6 Mbps)

14. Calculate the DIFS and PIFS for 802.11a (T_SIFS = 16 µs, T_slot = 9 µs).

15. In the contention window exponential back-off, if CWmin = 15 and two consecutive collisions occur, what is the current CW?

---

## PART 5: EXAM-READY QUICK REFERENCE

**PHY mnemonics:**
- **b**asic → **b**igger channels (HR-DSSS, 11 Mbps, 2.4 GHz)
- **a**dvanced → **a**nother band (OFDM, 54 Mbps, 5 GHz)
- **g**ood compatibility → **g**oes 2.4 GHz with OFDM too
- **n**ice MIMO → **n**ext level (HT, up to 600 Mbps, 2.4+5 GHz)
- a**x** = e**x**cellent efficiency (OFDMA + MU-MIMO + BSS coloring)

**IFS priority order (high → low):** SIFS → PIFS → DIFS → AIFS[VO] → AIFS[BE]

**Back-off freezes when:** another station transmits during your back-off count → timer pauses → resumes when medium idle (fairness mechanism)

**RTS/CTS duration chain:**
- RTS.duration = T_CTS + T_data + T_ACK + 3×SIFS
- CTS.duration = T_data + T_ACK + 2×SIFS
- DATA.duration = T_ACK + SIFS (0 if no more fragments)

**Aggregation rule of thumb:** A-MPDU gives slightly less throughput than A-MSDU because MPDUs each have their own MAC+LLC headers, while A-MSDU subframes only add small subframe headers. However, A-MPDU allows partial retransmission.

**802.11ax key insight:** Previous Wi-Fi generations increased the pipe (speed). Wi-Fi 6 improves how multiple users share the pipe (efficiency), which is what matters in dense environments like stadiums, offices, and apartments.
