# MOBAN Chapter 9 — Recent Evolutions
# Study Guide

---

## Outline

1. [6G Visions](#1-6g-visions)
2. [Deterministic Connected Systems](#2-deterministic-connected-systems)
3. [Time-Sensitive Networking (TSN)](#3-time-sensitive-networking-tsn)
4. [Mission-Critical Communications](#4-mission-critical-communications)
5. [Wi-Fi — What's Next?](#5-wi-fi--whats-next)
6. [Joint Sensing & Communications](#6-joint-sensing--communications)

---

## 1. 6G Visions

### 1.1 The Landscape

Multiple competing 6G visions exist (ITU, SNS IA, HEXA-X, etc.). There is **no single agreed-upon 6G standard yet** — the name refers to a collection of visions targeting the 2030s.

Key themes across all visions:
- Instantaneous interactions and trustworthy control
- Integration of communication, sensing, computation, and AI
- Coverage beyond current 5G, into new frequency ranges
- New service category proposed: **HRLLC** (Hyper-Reliable Low-Latency Communications)

### 1.2 6G Spectrum

**FR3 — Centimeter waves (7–15 GHz):** The main candidate band for 6G.

> **Why centimeters, not mmWave?**
> - mmWave 5G deployments have largely **failed for mobile traffic**
> - In the US: average time connected to mmWave 5G is **< 1%** of total connected time
> - South Korea cancelled 28 GHz spectrum allocations due to operator "lack of spending"
> - mmWave is viable only for **Fixed Wireless Access (FWA)** — not general mobile use

| Band | Frequencies | Role |
|---|---|---|
| Sub-6 GHz | < 6 GHz | Wide coverage (same as 5G) |
| FR3 / Centimeter wave | 7–15 GHz | 6G primary mobile band |
| sub-THz | 90–300 GHz | Massive data, very short range (niche) |

### 1.3 The Spectrum Battle

Key tension: **exclusive licensed spectrum vs. shared/unlicensed**
- Coverage is limited by **spectrum/regulation**, not technology
- Private licensed spectrum is **technology-neutral** — any standard could use it
- 5G is encroaching into unlicensed Wi-Fi bands (e.g., 6 GHz)
- Question: should we decouple spectrum from technology? Run Wi-Fi in licensed bands?

---

## 2. Deterministic Connected Systems

### 2.1 What Is Determinism?

A **deterministic** communication system offers **bounded, guaranteed** end-to-end latency and reliability — not just "best effort" or statistical guarantees.

Current commercial 5G and Wi-Fi do **NOT** offer true deterministic guarantees (they are eMBB-focused).

### 2.2 Use Cases Requiring Determinism

| Domain | Latency Target | Example |
|---|---|---|
| Industrial automation | 20 µs – 10 ms | Machine-to-machine control |
| Automotive | Real-time | Safety-critical in-vehicle systems |
| Audio/video | Low latency | Professional multimedia production |
| Holographic communications | < 20 ms + Gbps | Immersive interactions |
| Tactile Internet | < 5 ms | Human-machine haptic interaction |
| Mission-critical | < 10–100 ms | Emergency/defense operations |
| Collaborative robotics | 10–100 ms | Remote decision-making |

### 2.3 Candidate Technologies for Deterministic Communication

| Technology | Spectrum | Use |
|---|---|---|
| Wired Ethernet (TSN) | — | Industrial LAN backbone |
| Wi-Fi (specialized) | Unlicensed / local licensed | 80–90% of indoor data |
| 5G (specialized) | Global/local licensed | Outdoor + some indoor |

> **Key insight:** 80% of data is consumed **indoors**. 80–90% of all data traffic goes over Wi-Fi (unlicensed). Cellular 5G carries only 10–20% of total traffic. This makes **Wi-Fi the primary target** for deterministic wireless.

### 2.4 The Problem with COTS Chips

**COTS (Commercial Off-The-Shelf)** chips are:
- Black-box designs with limited control
- Optimized for mass-market (eMBB) use cases
- Not reconfigurable for specialized deterministic needs

→ Specialized and customizable hardware platforms are needed (e.g., FPGA-based SDR).

---

## 3. Time-Sensitive Networking (TSN)

### 3.1 What is TSN?

**TSN** is a set of **IEEE 802.1 standards** extending Ethernet to provide deterministic, time-synchronized, bounded-latency communication.

Goal: **End-to-end reliable and latency-bounded connectivity**

Key standards:
| Standard | Purpose |
|---|---|
| **IEEE 802.1AS** | Time synchronization (precision clock) |
| **IEEE 802.1Qbv** | Time-aware scheduling (gating mechanism) |

### 3.2 Wired TSN → Wireless TSN

The challenge: extend TSN guarantees from wired Ethernet **over wireless links** (Wi-Fi or 5G).

#### 5G-TSN Integration (5G URLLC)

Architecture: 5G system acts as a **logical TSN bridge** between two TSN segments.
- DS TT (Device Side TSN Translator) at the UE
- NW TT (Network Side TSN Translator) at the UPF
- CNC (Central Network Controller) manages the TSN schedule

> **Critical limitation:** The current 5G-TSN architecture is **NOT truly deterministic**!
> - Target: µs timing offset, < 1 ms latency
> - Reality: 5G was designed for eMBB, not TSN. URLLC features (preemption, mini-slots, retransmissions) are layered onto a "massively complex design that does not scale down well."

#### Wireless TSN over Wi-Fi (UGent IDLab approach)

The **openwifi** platform: a customizable Wi-Fi implemented on FPGA (System-on-Chip).
- Full white-box design: from digital baseband to Linux driver
- High-speed on-chip bus: ping RTT of **300 µs** (vs. 1 ms in COTS Wi-Fi)
- Enables full low-level customization not possible with COTS chips

Key achievements:
| Feature | Performance |
|---|---|
| Time synchronization | µs-level accuracy |
| IEEE 802.1Qbv gating | Implemented over Wi-Fi |
| Bounded E2E latency | **< 3.25 ms** (COTS Wi-Fi: ~70 ms) |
| Seamless mobility (handover) | < 9 ms with UWB + ML support |
| Impactless association | Beacon-based, no traffic disruption |
| In-band telemetry (INT) | Real-time E2E latency, RSSI, retransmissions |

**Three patented techniques:**
1. Device and Method for clock synchronization in a wireless network
2. Impactless association in a wireless time-sensitive network
3. Impactless hand-over in a wireless time-sensitive network

### 3.3 Deterministic Systems Vision (Summary)

> *"Enabling communication stacks and connectivity services with deterministic end-to-end guarantees using programmable wireless & wireline hardware — offering guarantees beyond speed & capacity in complex, dynamic, and mobile environments."*

Philosophy:
- Move away from **one-size-fits-all** (mass-market) solutions
- Target high-end, smaller-volume **professional markets**
- Use a subset of standardized + pre-standard features
- **Prove it** — no theoretical claims without hardware demonstration

---

## 4. Mission-Critical Communications

### 4.1 BOLSTER Project (B5G Tactical Network)

**Goal:** Beyond-5G network for private mobile standalone tactical (military/defense) use.

Key features:
- **Zero-touch** network deployment and management
- Standalone B5G mounted in a vehicle
- Extended coverage via **mobile base station** (urban and rural)
- Ruggedized UEs + surveillance drones

**Technology stack:**
- Citymesh vehicle with Athonet 5G core + Nokia 5G RAN
- IMEC SDR platform for:
  - **AI/ML-powered technology recognition & traffic characterization**
  - Intelligent Radio Resource Management (RAN slicing, interference mitigation)
  - PHY-layer encryption via **CSI obfuscation**

### 4.2 AI/ML Technology Recognition

**Semi-supervised learning** for identifying which radio technology is transmitting (Wi-Fi, 5G, LTE, interference, unknown).

- Patent: EP4026059A1 — Neural Network for Identifying Radio Technologies
- Used to detect interference and steer traffic to the right radio resource

### 4.3 O-RAN Integration

**O-RAN** (Open RAN): open, disaggregated base station architecture.

Key components:
- **O-CU** (Open Central Unit), **O-DU** (Open Distributed Unit), **O-RU** (Open Radio Unit)
- **Near-RT RIC** (Real-Time RAN Intelligent Controller): < 10 ms control loop
- **Non-RT RIC** / SMO: > 1 s control loop (policy, ML model management)
- **A1 interface** (policy), **E2 interface** (control)

Use case: Intelligent scheduler uses TRTC (Technology Recognition & Traffic Characterization) to detect interference and prioritize high-priority slices in real time.

### 4.4 5G/6G + Wi-Fi Integration

**Goal:** Intelligent, real-time integration of 3GPP (5G/6G) and IEEE 802.11 (Wi-Fi/TSN) under the O-RAN framework.

Provides:
- Optimal wireless technology selection and traffic steering
- Real-time monitoring, control, and optimization
- Rapid automated deployment in complex environments
- Improved privacy and security

---

## 5. Wi-Fi — What's Next?

### 5.1 Wi-Fi Context

- > 21 billion Wi-Fi devices in use today
- Wi-Fi carries **> 50% of all internet traffic**
- Present in virtually every indoor environment
- No indoor environment without Wi-Fi

### 5.2 Wi-Fi 7 (IEEE 802.11be) — Extremely High Throughput (EHT)

Wi-Fi 7 is finalized and being deployed now.

**Key new features:**

| Feature | Description |
|---|---|
| **Multi-Link Operation (MLO)** | Combine 2+ Wi-Fi bands (2.4 / 5 / 6 GHz) into one logical link. Increases throughput and reliability. Modes: non-simultaneous or simultaneous Tx/Rx |
| **Preamble Puncturing** | Transmit on wide channels (80/160 MHz) even if part of the channel is occupied by another AP. Punctures busy 20 MHz sub-channels |
| **Restricted TWT (R-TWT)** | Reserved contention-free time intervals negotiated between AP and client. Other devices cannot transmit during R-TWT windows → determinism for latency-sensitive flows |
| **4096-QAM** | Higher modulation order → more bits per symbol |
| **320 MHz channels** | In 6 GHz band |
| **Multi-RU (MRU)** | Flexible resource unit allocation in OFDMA |

**R-TWT mechanism:**
```
Time →
AP:     [BEACON] ... [negotiate R-TWT] ... [trigger] [data] [trigger] [data]
Client:                                               [data]           [data]
Others: ────────────────────── NO TX ALLOWED ──────────────────────────────
```

### 5.3 Wi-Fi 8 (IEEE 802.11bn) — Ultra-High Reliability (UHR)

Wi-Fi 8 is under standardization (target: late 2020s). Focus: **reliability** for high-criticality use cases.

**Key new features:**

| Feature | Purpose |
|---|---|
| **Multi-AP Coordination** | Multiple APs work together, coordinated via trigger frames (< 50 ns sync accuracy over optical backhaul) |
| **Coordinated Spatial Reuse (C-SR)** | APs coordinate simultaneous transmissions to different STAs, reducing interference. Up to **50% goodput increase** |
| **Coordinated Beamforming** | APs steer beams jointly to avoid inter-BSS interference |
| **Coordinated TDMA** | APs share time slots to eliminate collision |
| **Coordinated R-TWT** | Coordinated contention-free windows across multiple APs |
| **Distributed MLO** | Handle roaming by switching between AP links seamlessly (< 9 ms with ML support) |
| **Seamless Roaming** | No packet loss during AP handover |
| **Enhanced Long Range PPDU** | Extended range transmissions |
| **Non-Primary Channel Access (NPCA)** | Use secondary channels opportunistically |
| **Distributed Resource Units (dRU)** | Flexible OFDMA across multiple APs |

**Multi-AP Coordination architecture:**
```
AP 1 ──── Coordination (backhaul) ──── AP 2
  |                                       |
STA 1                                 STA 2
       ← C-OFDMA trigger from AP 1 →
              (simultaneous UL)
```

### 5.4 Industry Standardization Timeline

```
Wi-Fi 6 (802.11ax) → Wi-Fi 7 (802.11be) → Wi-Fi 8 (802.11bn) → ...
    2019                  2024                  ~2028
 OFDMA, TWT           MLO, R-TWT            Multi-AP, C-SR
```

---

## 6. Joint Sensing & Communications

### 6.1 What Is ISAC?

**ISAC (Integrated Sensing And Communications):** Using the same radio hardware and signals for **both communication and sensing/radar** simultaneously.

This is a key 6G capability — the radio network becomes a sensor network.

### 6.2 RF Sensing Approaches

| Approach | Method | Use |
|---|---|---|
| **Passive spectrum sensing** | Analyze IQ samples from SDR | Classify known tech; detect unknown/anomalies |
| **Active radar sensing** | Use Wi-Fi or 5G signals as radar | Detect humans, objects, movement |
| **AI-powered analysis** | Foundation models, transformers, U-NET, CNNs | Higher accuracy, generalization |

**RF situational awareness:** Combine passive + active sensing for a complete picture of the radio environment.

### 6.3 Wi-Fi Radar

The communication signal itself can be used as a radar:
- **Monostatic SIMO Wi-Fi Radar**
- AoA (Angle of Arrival) resolution: **~6°**
- Distance change detection: **~1.37 mm**
- Can track the location and movement of other Wi-Fi devices

### 6.4 Secure Sensing — CSI Obfuscation

**Problem:** Standard communication signals can be **misused** for sensing (e.g., someone tracking your location through walls using your Wi-Fi).

**Solution: CSI Fuzzer**
- Extension of the communication system
- Adds controlled noise/perturbation to CSI (Channel State Information)
- **Only authorized parties** can perform sensing → unauthorized sensing is prevented

### 6.5 AI Wireless Foundation Models

A **foundation model** is a large model pre-trained on massive unlabeled data that can be fine-tuned for many downstream tasks (analogous to GPT/BERT for language).

Applied to wireless: a single model trained on diverse RF data could handle:
- Technology recognition
- Channel estimation
- Anomaly detection
- Beam management
- ... all without task-specific training from scratch

---

## Key Takeaways

| Topic | Core Message |
|---|---|
| **6G** | No single vision; centimeter waves (7–15 GHz) as primary band; mmWave has failed for mobile |
| **Determinism** | Current 5G/Wi-Fi are NOT deterministic; specialized hardware (FPGA/SDR) is needed |
| **TSN** | IEEE 802.1AS (sync) + 802.1Qbv (gating); 5G-TSN exists but is not truly deterministic; Wi-Fi TSN with < 3.25 ms achievable |
| **Mission-critical** | O-RAN + AI/ML for intelligent, adaptive, self-managing tactical networks |
| **Wi-Fi 7** | MLO + R-TWT + preamble puncturing → higher throughput and first steps toward determinism |
| **Wi-Fi 8** | Multi-AP coordination (C-SR, C-TDMA) → ultra-high reliability for critical applications |
| **Joint sensing** | Same radio = communication + radar; CSI obfuscation for privacy; AI foundation models for RF |

---

*Study guide created for MOBAN Chapter 9: Recent Evolutions — UGent IDLab (Ingrid Moerman, Jeroen Hoebeke)*
