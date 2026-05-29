# MOBAN Chapter 7 — Part 1: GSM
# Comprehensive Study Guide

---

## PART 1: THEORY SUMMARY

---

### 1. Introduction

#### 1.1 Mobile Telephony — History

| Era | Name | Period | Features |
|-----|------|--------|---------|
| Pre-cellular | 0G | 1960s | Closed systems (police/fire/taxi); car-mounted; PSTN-connected; manual operation; no handover/roaming |
| Analog cellular | 1G | 1970s-80s | First handheld phones; many incompatible national systems; frequency reuse introduced |
| Digital cellular | 2G+ | 1990s→ | Voice encoded digitally; GSM = dominant 2G standard; data and SMS added |

#### 1.2 Analog vs. Digital

| Aspect | Analog (1G) | Digital (2G / GSM) |
|--------|------------|-------------------|
| Voice encoding | Modulated directly to RF | Sampled, compressed, error-coded |
| Quality vs. distance | Continuous degradation | Roughly constant, then sudden drop |
| Capacity | Lower | Higher (smaller cells, frequency reuse) |
| Extra services | Voice only | Data, fax, SMS |
| Security | None | Encryption, authentication, fraud prevention |

#### 1.3 Why GSM?

- European standard (CEPT 1982 → ETSI 1988) — wanted **single roaming-compatible standard** across Europe
- Also adopted worldwide
- **GSM = Global System for Mobile communications**
- 1991: GSM phase 1 recommendations (~5000 pages)
- 1992: first commercial service; 1998: >100 million subscribers

---

### 2. General GSM Properties — Multiple Access

GSM uses **three layers** of multiple access simultaneously:

| Technique | Name | GSM parameters |
|-----------|------|---------------|
| **SDMA** | Space Division Multiple Access | Cells with directional antennas (sectors) |
| **FDMA** | Frequency Division Multiple Access | 124 frequencies, channel spacing = **200 kHz** |
| **TDMA** | Time Division Multiple Access | **8 time slots** per frame, frame duration = **4.615 ms** (= 60/13 ms), slot duration = **0.577 ms** (= 15/26 ms) |

**Duplex method: FDD (Frequency Division Duplex)**

| Direction | Frequency band |
|-----------|---------------|
| Uplink (MS → BTS) | 890 – 915 MHz |
| Downlink (BTS → MS) | 935 – 960 MHz |
| Duplex distance | **45 MHz** |

**Bitrates:**

| Bitrate | Value |
|---------|-------|
| Speech (codec) | 13 kbit/s |
| Channel bitrate | **270.9 kbit/s** |
| Bits per frame | 1250 bits (156.25 bits/slot) |

> A physical channel = 1 specific frequency + 1 specific time slot.
> With 124 frequencies × 8 slots = **992 physical channels** total.

---

### 3. Architecture

#### 3.1 Overall Structure

GSM network is organized into three subsystems:

```
MS <--air--> BTS <--Abis--> BSC <--A--> MSC <---> PSTN/HLR/VLR
                                         |
                                       GMSC
```

| Subsystem | Components | Purpose |
|-----------|-----------|---------|
| **RSS** (Radio Subsystem) | MS, BTS | Air interface and radio |
| **BSS** (Base Station Subsystem) | BTS + BSC | Radio resource management |
| **NSS** (Network & Switching Subsystem) | MSC, HLR, VLR, GMSC | Switching, routing, mobility |
| **OSS** (Operations Support System) | OMC, EIR, AuC | Management and security |

#### 3.2 Mobile Station (MS)

- All user equipment + software needed for GSM communication
- = **Terminal Equipment (TE)** + **Mobile Termination (MT)**
  - MT performs all network-specific tasks (TDMA, FDMA, coding, encryption) and provides data interface
- Types: fixed (up to 20W), portable (up to 8W), handhelds (up to 2W)

**Major MS functions:**
- Voice and data transmission
- Frequency and time synchronization
- Monitoring power and signal quality
- Equalization of multipath distortion
- Location updates
- SMS display (max 160 characters)
- **Timing advance** — compensates for varying propagation delay

**SIM Card (Subscriber Identity Module):**
- Smart card with microprocessor, RAM and ROM
- Binds subscriber identity to the device (phone not operational without SIM, except emergency calls)
- Stores:
  - IMSI, MS-ISDN (subscriber identity and phone number)
  - Personal data (phone book), optional SMS
  - Authentication key and algorithms
  - Dynamic info: TMSI, LAI
  - PIN code (and PUK for unblocking)

#### 3.3 Base Transceiver Station (BTS)

- Serves a **single cell** (radius: few 100m to 35 km; typically 3 sectors per BTS)
- Functions:
  - Signal processing: encoding, encryption, modulation, multiplexing
  - **TRAU** (Transcoder/Rate Adaptation Unit): converts 13 kbps voice from MS to 64 kbps A-Law for the backhaul
  - Connected to BSC via Abis interface (typically 2 Mbps links)
  - Emits time and frequency sync signals (FCCH, SCH)
  - Controls frequency hopping
  - Detects random access (RACH) from new MSs
  - Timing advance management
  - Uplink radio measurement (to determine best cell for MS)

#### 3.4 Base Station Controller (BSC)

- Controls typically **10 to 100 BTSs**
- Functions:
  - **Radio Resource (RR) management** for its BTSs
  - Inter-cell handover within its BTSs
  - Reallocation of frequencies between BTSs
  - Power management of BTSs and MSs (2 dB steps)
  - Distributes time/frequency sync signals to BTSs
  - Controls frequency hopping between BTSs
  - **Traffic concentration** toward MSC
  - Interface to OMC

#### 3.5 Mobile Switching Center (MSC)

- High-performance digital ISDN switch = **fixed backbone** of GSM
- Functions:
  - Interworking with PSTN (including switching)
  - Call set-up coordination (including paging of MS)
  - Location registration using VLR
  - Handover management between different BSSs
  - Billing for all subscribers in its area
  - Transfers encryption parameters between VLR and BSS
  - Reallocation of frequencies to BTSs within different BSSs
  - Echo canceler control (GSM roundtrip delay > 180 ms)
  - Gateway to SMSC (Short Message Service Center)

- **GMSC** (Gateway MSC): MSC connected to PSTN; entry/exit point for external calls

#### 3.6 Home Location Register (HLR)

- Specialized database — **permanent subscriber data** for the home PLMN

| Data type | Content |
|-----------|---------|
| Permanent | IMSI, MS-ISDN, subscribed services, roaming restrictions, forwarded-to number, GPRS settings, authentication key |
| Temporary | Current MSRN, current VLR, current MSC, current LA, temporary auth/encryption data |

- Updated whenever MS leaves its LA

#### 3.7 Visitor Location Register (VLR)

- Dynamic database — **data of current users** in the MSC's Location Area
- When MS enters a new LA: VLR copies relevant data from HLR
- Functions:
  - Allocates TMSI numbers
  - Provides MSRN for routing calls
  - Stores current LA where MS is registered
- **Goal of HLR/VLR hierarchy**: avoid frequent HLR updates and long-distance signaling

#### 3.8 Equipment Identity Register (EIR)

- Part of OSS; stores **IMEI** numbers in three lists:
  - **White**: valid IMEIs
  - **Black**: stolen devices
  - **Grey**: malfunctioning devices

#### 3.9 Authentication Center (AuC)

- Part of OSS (often a protected part of HLR)
- Provides authentication and encryption support
- Stores algorithms and keys
- **Goal**: protect communication over the air interface

#### 3.10 Operations and Maintenance Center (OMC)

- Part of OSS; controls and monitors all other network entities
- Operates typically at MSC level (several OMCs per PLMN)
- Functions: alarm handling, software/config control, fault management, performance management, network status reports, traffic monitoring

---

### 4. Mobile Identification Numbers

| Number | Full name | Description | Where stored |
|--------|-----------|-------------|-------------|
| **IMSI** | International Mobile Subscriber Identity | Unique ID of subscriber (not equipment); used GSM-internally | SIM card, HLR |
| **TMSI** | Temporary Mobile Subscriber Identity | Temporary IMSI used at VLR for security (hides real IMSI over air) | SIM card, VLR |
| **MS-ISDN** | Mobile Subscriber ISDN Number | Public phone number (what you dial) | SIM card, HLR |
| **MSRN** | Mobile Station Roaming Number | Temporary number assigned by VLR; used by HLR to route calls to roaming MS | HLR, VLR |
| **IMEI** | International Mobile Equipment Identity | Unique identity of the device (dial *#06#) | EIR, device |
| **LAI** | Location Area Identity | Identifies a Location Area (group of cells); broadcast on BCCH | HLR, BTS |
| **CI** | Cell Identity | Unique ID of a single cell (2×8 bit hex); Global Cell ID = LAI + CI | BTS |

**Location Area (LA):** one or more cell groups; all cells in LA paged when MS is called.

#### Call Routing Example

```
Caller dials MS-ISDN
    --> GMSC (home PLMN)
    --> HLR: knows current VLR/MSC
    --> VLR: issues MSRN
    --> GMSC uses MSRN to route to current MSC
    --> MSC pages MS via PCH in its LA
    --> MS responds via RACH
    --> Connection set up
```

---

### 5. Physical Layer

#### 5.1 Logical Channels

GSM defines **logical channels** on top of the physical TDMA/FDMA medium:

**Traffic Channels (TCH):** bidirectional, dedicated to a single user

| Channel | Type | Bitrate | Usage |
|---------|------|---------|-------|
| **TCH/F** | Full rate | 22.8 kbit/s | Speech at 13 kbit/s (FS) or 12.2 kbit/s (EFR); data at 14.4/9.6/4.8 kbit/s |
| **TCH/H** | Half rate | 11.4 kbit/s | Speech at 6.5 kbit/s; doubles capacity, lower quality |

**Broadcast Control Channels (BCCH):** downlink only, BTS to all MSs

| Channel | Purpose |
|---------|---------|
| **BCCH** | Cell-specific info: power levels, country code, PLMN code, LAI, CI, RF channels used, neighboring cells, hopping sequences |
| **FCCH** | Frequency Correction Channel — allows MS to accurately tune to BTS |
| **SCH** | Synchronization Channel — provides TDMA frame synchronization to MS |

**Common Control Channels (CCCH):** connection setup between MS and BTS

| Channel | Direction | Purpose |
|---------|-----------|---------|
| **PCH** (Paging Channel) | Downlink (point-to-multipoint) | BTS pages MS when incoming call |
| **RACH** (Random Access Channel) | Uplink (point-to-point) | MS requests SDCCH allocation; uses **slotted Aloha** |
| **AGCH** (Access Grant Channel) | Downlink (point-to-point) | Allocates SDCCH or TCH to MS |

> PCH + AGCH = PAGCH

**Dedicated Control Channels (DCCH):** point-to-point bidirectional

| Channel | Purpose |
|---------|---------|
| **SDCCH** | Stand-alone Dedicated Control Channel — used for call setup before TCH assigned; registration, authentication |
| **SACCH** | Slow Associated Control Channel — linked to TCH/SDCCH; continuous measurement reports (signal strength, channel quality) |
| **FACCH** | Fast Associated Control Channel — linked to TCH; "stealing mode" — takes slots from TCH for urgent signaling (handover, call finalization) |

#### 5.2 Connection Set-up Flow

**Mobile Terminated Call (MTC):**
```
1. Caller dials MS-ISDN
2. Call forwarded to GMSC
3. GMSC signals HLR
4-5. HLR requests MSRN from VLR
6. HLR sends MSC info to GMSC
7. GMSC forwards call to current MSC
8-9. MSC checks MS status with VLR
10-11. MS paged via PCH in all cells of LA
12-13. MS answers (via RACH --> SDCCH assigned via AGCH)
14-15. Security checks (authentication)
16-17. TCH assigned; FACCH used for final setup; voice call begins
```

**Mobile Originated Call (MOC):**
```
1. MS sends channel request via RACH --> SDCCH assigned via AGCH
   MS sends service request over SDCCH
2. Request forwarded to MSC
3-4. MSC checks authorization and resource availability
5-10. Connection established if resources available
```

#### 5.3 Frame Structure

**Hierarchy:**

```
Timeslot (0.577 ms)
  --> TDMA Frame (8 slots, 4.615 ms)
    --> Multiframe (26 or 51 frames)
      --> Superframe (26 x 51 = 1326 frames, ~6.12 s)
        --> Hyperframe (2048 superframes, ~3.5 h)
```

| Multiframe | # Frames | Duration | Used for |
|-----------|---------|---------|---------|
| **26-multiframe** | 26 | ~120 ms | TCH + SACCH + FACCH (traffic) |
| **51-multiframe** | 51 | ~235 ms | BCCH, CCCH, SDCCH (signaling) |

**26-multiframe (traffic cycle):**
- 24 slots for TCH/F
- 1 slot for SACCH
- 1 idle slot
- All use the **same time slot number (TN)**

**51-multiframe (signaling cycle):**
- 5 × FCCH slots, 5 × SCH slots
- 5 × 4 BCCH slots, 5 × 4 PAGCH slots
- 1 idle slot
- Always uses **TN0**

**Why two multiframe lengths (26 and 51)?**
- 26 and 51 are **not multiples** of each other
- Unused slots in the 26-multiframe allow the MS to monitor different slots of the 51-multiframe cycle in turn
- MS can scan neighboring cells on FCCH/SCH to support handover measurements

**Hyperframe:**
- Longest cycle (~3.5 hours)
- Frame number used as input for encryption → provides high security variety

**Two numbering levels:**
- **TN** (Timeslot Number): 0 to 7
- **FN** (Frame Number): 0 to 2,715,647

#### 5.4 Channel Combinations in Cells

**Medium-capacity cell (4 transceivers = 32 physical channels):**
- TN0: FCCH + SCH + BCCH + CCCH (51-superframe)
- TN2, TN4: SDCCH/8 + SACCH/8 (51-superframe)
- 29 × TN: TCH/F + FACCH/F + SACCH (26-superframe)
- → **29 simultaneous phone calls**

**High-capacity cell (12 transceivers = 96 physical channels):**
- TN0-TN6 (4×): BCCH + CCCH (51-superframe)
- 5 × TN: SDCCH/8 + SACCH/8 (51-superframe)
- 87 × TN: TCH/F + FACCH/F + SACCH (26-superframe)
- → **87 simultaneous phone calls**

#### 5.5 Frequency Hopping

**Why frequency hopping?**

1. **Mitigate Rayleigh fading**: amplitude varies by frequency and position; hopping ensures consecutive bursts of a call are not all affected the same way → better error correction
2. **Interferer diversity**: frequency reuse (SDMA) causes inter-cell interference; hopping spreads the disturbance across frequencies

**Slow frequency hopping:**
- Frequency may change every **burst** (every frame = every 4.615 ms)
- Rate: **216.7 hops/s**
- Channel = fixed timeslot, hopping frequency
- FCCH/SCH/BCCH/PAGCH always on **fixed beacon frequency** (no hopping)
- TCH groups hop in an ordered/pseudo-random way

#### 5.6 Bidirectional Transmission & Timing Advance

**FDD:** uplink and downlink frequencies shifted by **45 MHz**.

**Slot timing (from MS point of view):**
- MS receives on downlink; transmits on uplink **3 time slots later** (same TN numbering)
- MS always transmits and receives at different times → **half duplex** in time

**Timing Advance:**
- Problem: MS at different distances from BTS → different propagation delays → risk of uplink bursts from adjacent slots overlapping at BTS
- Solution: MS transmits **earlier** by an offset calculated by BTS
- Timing advance = 6-bit value sent by BTS as correction
- 30 km distance ≈ 0.1 ms propagation delay → significant offset needed

---

### 6. Data Link Layer

**Major functions:**
- Provide link connections to exchange signaling information (MS ↔ BTS ↔ BSC ↔ MSC ↔ VLR ↔ HLR)
- Support SMS messaging service

**Aspects:**
- Define frame formats
- Error detection and correction of transmitted frames
- Multiplexing
- Flow control

> Note: the Data Link Layer does **NOT** handle the actual transfer of user voice or data — that is at the Physical Layer.

---

### 7. Network Layer

The Network Layer deals with **signaling exchanges** to provide GSM's core services. It has **three sub-layers**:

```
+------------------------------------------+
|  CM  : Communication Management          |
+------------------------------------------+
|  MM  : Mobility (& Security) Management  |
+------------------------------------------+
|  RR  : Radio Resource Management         |
+------------------------------------------+
```

#### 7.1 Radio Resource Management (RR)

**Scope:** Establishes and releases connections between MS and MSC; mainly related to the **radio interface**.

**Functionalities:**
- Dynamic channel allocation (cope with limited radio resources)
- Coordination of handover between cells
- Channel assignment and release procedures
- Channel change and handover (always initiated by **network**, not MS)
- Change of channel frequencies, hopping sequences, frequency tables
- Measurement reports from MS
- Power control and timing advance
- Cipher mode setting

**Radio Resource Session:** period between two idle states of the MS — transmission path between MS and anchor MSC. Path can be modified (handover) during the session.

#### 7.2 Handover

**Definition:** MS releases connection with current BTS and establishes connection with new BTS while maintaining the ongoing call.

**Classification by motivation:**

| Type | Trigger | Measurement basis |
|------|---------|------------------|
| **Rescue handover** | Transmission quality too low (most common) | Error rate, path loss, delay — measured by MS and BTS |
| **Confinement handover** | Optimize global interference | Path loss to other cells + knowledge of used frequencies |
| **Traffic handover** | Reduce congestion | Load of each BTS |

> Maximum supported speed: **250 km/h**
> Decision and target cell choice are **operator-dependent** (not mandated by GSM spec).

**Measurement process:**
- All neighboring cells broadcast constant power on their **beacon frequency** (FCCH + SCH + BCCH)
- MS learns neighboring cell frequencies from current cell's BCCH
- MS measures neighbor cells when **NOT transmitting/receiving** (during idle slots of 26-multiframe)
- 26 vs. 51 multiframe mismatch allows scanning all neighbors over time

**Classification by topology:**

| Type | Scope | Complexity |
|------|-------|-----------|
| **Inter-BTS (intra-BSC)** | Between BTSs of same BSC | Low — handled by BSC |
| **Inter-BSC (intra-MSC)** | Change BSC, may imply LA update | Medium |
| **Inter-MSC** | Change MSC | High — most complex |

**Timing advance in handover:**
- **Synchronous HO**: cells synchronized → MS can compute timing advance
- **Asynchronous HO**: timing advance must be initialized from scratch

#### 7.3 Power Control

- Adapts transmission power on radio interface — both **MS and BTS** (independently)
- Goal: improve spectral efficiency + extend battery life
- Good reception → reduce power while maintaining quality
- Managed by BSS based on MS/BTS measurements
- Commands in **2 dB steps every 60 ms**

#### 7.4 Mobility Management (MM)

**Scope:** Keeps track of MS while moving; handles security.

**Tasks:**
- Support user mobility (roaming; not radio-related)
- MS: choose appropriate cell and network
- Infrastructure: get and update location data to route calls
- Security: registration, identity checking, service authorization, user confidentiality (TMSI allocation), user authentication

**Location Update procedure:**
```
1. MS detects new LAI on BCCH (different from stored LAI) → requests location update (sends IMSI)
2. MSC/VLR sends update to HLR
3. HLR removes MS from previous MSC/VLR
4. HLR confirms to new MSC/VLR
5. MSC/VLR confirms to MS (issues new TMSI)
```

> HLR, VLR, and SIM are all involved in maintaining consistency as MS moves.

#### 7.5 Communications Management (CM)

**Scope:** Call control — very similar to ISDN/PSTN call control.

**Tasks:**
- **Call Control (CC):** routing of mobile-terminating calls; setup of mobile-originating/terminating calls; emergency calls; call release; DTMF signaling; in-call modification
- **Supplementary Services (SS)** management: call forwarding, call waiting, etc.
- **SMS** (Short Message Service)

**Routing:**

| Number | Role in routing |
|--------|----------------|
| MS-ISDN | Caller dials this — sent to GMSC of home PLMN |
| IMSI | Used internally to query HLR for current location |
| MSRN | Temporary geographic routing number; issued by VLR; used to route call from GMSC to correct MSC |

> MSRN has **geographical meaning** — it encodes the MSC location so the call can be routed there.

---

## PART 2: SUMMARY TABLES & QUICK REFERENCE

---

### Architecture Components

| Component | Subsystem | Key role |
|-----------|----------|---------|
| MS | RSS | User terminal; SIM-based identity |
| BTS | BSS | Radio transceiver; serves one cell |
| BSC | BSS | Radio resource management; controls 10-100 BTSs |
| MSC | NSS | Switching, routing, handover between BSSs |
| GMSC | NSS | Gateway to PSTN |
| HLR | NSS | Permanent subscriber database |
| VLR | NSS | Temporary database of current users in MSC area |
| EIR | OSS | Equipment identity register (white/black/grey lists) |
| AuC | OSS | Authentication and encryption keys |
| OMC | OSS | Network operations and maintenance |

---

### Identification Numbers

| ID | Scope | Permanent? | Stored in |
|----|-------|-----------|----------|
| IMSI | Subscriber (internal) | Yes | SIM, HLR |
| TMSI | Subscriber (local, security) | No | SIM, VLR |
| MS-ISDN | Subscriber (public phone nr) | Yes | SIM, HLR |
| MSRN | Routing (geographic, temporary) | No | HLR, VLR |
| IMEI | Equipment (device) | Yes | EIR, device |
| LAI | Location Area | Fixed per area | HLR, BTS |
| CI | Cell | Fixed per cell | BTS |

---

### Logical Channels Summary

| Channel | Type | Direction | Purpose |
|---------|------|-----------|---------|
| TCH/F | Traffic | Both | Full-rate speech/data |
| TCH/H | Traffic | Both | Half-rate speech/data |
| BCCH | Broadcast | DL | Cell info to all MSs |
| FCCH | Broadcast | DL | Frequency correction |
| SCH | Broadcast | DL | Frame synchronization |
| PCH | Common | DL | Paging MS |
| RACH | Common | UL | MS requests channel (slotted Aloha) |
| AGCH | Common | DL | Grant SDCCH or TCH |
| SDCCH | Dedicated | Both | Call setup before TCH assigned |
| SACCH | Dedicated | Both | Continuous measurements (slow) |
| FACCH | Dedicated | Both | Urgent signaling, steals TCH slots |

---

### GSM Frequency & Timing Parameters

| Parameter | Value |
|-----------|-------|
| Frequency band (UL) | 890 – 915 MHz |
| Frequency band (DL) | 935 – 960 MHz |
| Duplex distance | 45 MHz |
| Channel spacing | 200 kHz |
| Number of frequencies | 124 |
| Time slots per frame | 8 |
| Frame duration | 4.615 ms (= 60/13 ms) |
| Slot duration | 0.577 ms (= 15/26 ms) |
| Channel bitrate | 270.9 kbit/s |
| Speech bitrate | 13 kbit/s |
| Frequency hopping rate | 216.7 hops/s |

---

### Network Layer Sub-layers

| Sub-layer | Handles | Key mechanism |
|-----------|---------|--------------|
| **RR** | Radio connection, handover, power control | Initiated by network; measurement by MS |
| **MM** | Mobility tracking, security, location update | HLR/VLR/SIM cooperation |
| **CM** | Call control, SMS, supplementary services | ISDN-like call setup |

---

### Common Exam Pitfalls

1. **IMSI vs. MS-ISDN**: IMSI is the internal GSM subscriber ID (never given to callers); MS-ISDN is the public phone number.
2. **TMSI vs. IMSI**: TMSI is a temporary alias issued by VLR to protect IMSI privacy over the air.
3. **MSRN has geographical meaning**: it's used for routing — not just a random number.
4. **Handover is always initiated by the network** (BSC/MSC), not by the MS — the MS only reports measurements.
5. **RACH uses slotted Aloha** — the MS contends for access; collisions are possible.
6. **FACCH "steals" slots from TCH** — it takes over time slots that would otherwise carry voice during urgent signaling.
7. **26 and 51 multiframes are not multiples** — this is intentional to allow MS to scan neighbor cell signaling during idle slots.
8. **Timing advance is set by BTS** and communicated to the MS — the MS adjusts its transmit timing earlier to compensate for propagation delay.
9. **BSC handles intra-BSC handover**; inter-BSC handover involves the MSC; inter-MSC is the most complex.
10. **BTS converts 13 kbps speech to 64 kbps** via TRAU before sending to BSC — the backhaul interface runs at much higher rates than the air interface speech codec.
