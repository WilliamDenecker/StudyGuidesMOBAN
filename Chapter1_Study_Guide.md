# Chapter 1: Introduction to Mobile & Broadband Access Networks
## Comprehensive Study Guide — MOBAN 2025-2026

---

## Table of Contents
1. [Terminology](#1-terminology)
2. [Applications](#2-applications)
3. [Devices](#3-devices)
4. [Market & Statistics](#4-market--statistics)
5. [Wireless vs Fixed Networks](#5-wireless-vs-fixed-networks)
6. [Reference Model](#6-reference-model)
7. [Flash Cards](#7-flash-cards)
8. [Exam Checklist](#8-exam-checklist)

---

## 1. Terminology

### 1.1 Two Aspects of Mobility

| Concept | Definition |
|---|---|
| **User mobility** | User can access the same/similar services at different places — "anytime, anywhere, with anyone" |
| **Device portability** | The communication device itself moves (usually with the user) and the network adapts |

> **This course covers BOTH.** User mobility is about service continuity for the person; device portability is about network mechanisms ensuring the moving device stays connected (e.g., handover between base stations).

---

### 1.2 Wireless vs Mobile — The 2×2 Matrix

"**Wireless**" = how you access the network (no cable).
"**Mobile**" = whether you/your device moves.

| | **Fixed** (no movement) | **Mobile** (moves) |
|---|---|---|
| **Wired** | Desktop PC in an office | Laptop reconnected via Ethernet at different hotels |
| **Wireless** | WLAN in a historic building; BWA last-mile link | Smartphone, tablet, laptop on the move |

The most important and most studied case in this course: **Mobile + Wireless**.

---

### 1.3 Integration of Wireless into Fixed Networks

Wireless networks are NOT standalone islands — they are natural extensions of existing fixed infrastructure:

| Fixed Network | Wireless Extension |
|---|---|
| LAN (Ethernet) | IEEE 802.11 → WLAN |
| Internet (IP) | Mobile IP — extends IP to support mobility |
| WAN / PSTN (POTS) | GSM / UMTS / LTE / 5G interworking with fixed telephony |

---

## 2. Applications

### 2.1 Vehicles

**Standard services in vehicles:**
- Personal communication: GSM, UMTS (3G), LTE (4G), NR (5G), satellite
- Positioning: GPS
- Broadcasting: DAB (Digital Audio Broadcasting) — music, news, road conditions, weather
- Fleet management: maintenance & logistics info for buses, trucks, high-speed trains

**Intelligent Transportation System (ITS):**

| Type | Description | Example |
|---|---|---|
| **V2V** — Vehicle-to-Vehicle | Local ad-hoc network with nearby vehicles | Vehicles warn each other about accidents or hazards ahead |
| **V2I** — Vehicle-to-Infrastructure | Vehicle interacts with road infrastructure | Intelligent Speed Adaptation (ISA), emergency call, remote driving |
| **V2VRU** — Vehicle-to-Vulnerable Road User | Direct interaction with pedestrians/cyclists | Warning to truck driver when a cyclist is in blind spot at a crossroads |

---

### 2.2 Emergencies

- Efficient communication during interventions
- Early transmission of patient data from ambulance to hospital (early diagnosis, preparation)
- **Critical role**: In natural disasters (earthquake, tsunami, hurricane) or conflicts (war, terrorism), fixed infrastructure collapses → wireless ad-hoc networks are the **only** surviving communication

> **BOLSTER / Mobile 5G Tactical Network** (UGent research example):
> - Beyond 5G (B5G) architecture for ad-hoc deployable standalone network mounted in a vehicle
> - Athonet 5G core + Nokia 5G RAN + ruggedized UEs + surveillance drone
> - SDR platform for AI/ML-empowered radio resource management and interference mitigation
> - PHY-layer encryption via CSI (Channel State Information) obfuscation

---

### 2.3 Business — The Travelling Salesman

Goal: true "mobile office" — direct, always-on access to company databases and files.

**ABC = Always Best Connected** — the paradigm that a device should always use the highest-quality available technology, switching seamlessly as the user moves.

Typical technology handoff during a commute:

| Location | Technology | Typical speed |
|---|---|---|
| Home | LAN | ~1 Gbps |
| Home (wireless) | DSL + WLAN | ~100 Mbps |
| On the road | 2.5G / 3G / 4G / 5G | 40 kbps – 1 Mbps |
| Train | 3G / 4G / 5G | ~100 kbit/s |
| Gas station hotspot | WLAN / 4G | 10 Mbit/s |
| Office | LAN | 1 Gbps |

> Note: speeds shown are user-experienced, not theoretical maxima. Sharing the medium, propagation conditions, and interference all reduce real-world throughput.

---

### 2.4 Replacement of Wired Networks

Wireless used where cabling is impractical or impossible:
- **Remote sensors**: weather stations, seismic monitoring — too remote to wire
- **Trade shows/fairs**: dynamic infrastructure, fast setup
- **Historic buildings**: no drilling/cabling → wireless APs in corners

---

### 2.5 Infotainment & Education

- Outdoor Internet access
- Location-aware travel/museum guides: real-time, location-dependent info via local wireless + GPS
- Ad-hoc gaming networks: devices discover each other when in proximity

---

### 2.6 Industry 4.0 / Industry 5.0

Application areas in smart factories (per 5G-ACIA white paper):
- Logistics: supply and inventory management
- Robot and motion control
- Operations control and monitoring
- Localization of devices and assets

**Key conceptual distinction:**

| | Industry 4.0 | Industry 5.0 |
|---|---|---|
| **Driver** | Technology-driven | Value-driven |
| **Goal** | Flexibility, versatility, productivity | Human-centric; societal needs and responsibility |
| **Relationship** | Provides the technology | Sets the goals and guardrails |

> The two are complementary: 4.0 provides the push; 5.0 provides the direction.

---

## 3. Devices

Listed from lowest to highest performance (CPU, memory, display, input):

| Device | Key Characteristics |
|---|---|
| **Sensor** | Simplest device; transmits state (e.g., temperature, door open/close); minimal processing |
| **Embedded controller** | Controls appliances (keyboard, lamp, washing machine, coffee machine) via wireless signal |
| **Traditional mobile phone** | Simple text display; voice, data, SMS |
| **Smartphone** | Full-color touchscreen, browser, character recognition, simplified office apps |
| **Tablet** | Color touchscreen; simple versions of office software (text processing, spreadsheets) |
| **Laptop** | Full desktop performance; same OS and software — differs only in size, weight, battery |

**General trade-off**: Performance ↑ → energy consumption, weight, size, cost ↑

---

## 4. Market & Statistics

### 4.1 Evolution of IP Traffic (Cisco VNI, 2017–2022)

| Traffic type | 2017 share | 2022 share | CAGR |
|---|---|---|---|
| Wired/fixed | 48% | 21.1% | 15% |
| Wi-Fi | 43% | 56.8% | 37.3% |
| Cellular/mobile | 9% | 22.1% | 46% |

**Key trends:**
- Mobile + wireless now accounts for ~79% of all global IP traffic
- Operators increasingly **offload** cellular (licensed spectrum) traffic to Wi-Fi (unlicensed spectrum) to boost overall network capacity

---

### 4.2 Cellular Network Generations

```
1G → 2G (GSM) → 2.5G (GPRS) → 3G (UMTS) → 3.5G (HSPA) → 4G (LTE) → 5G (NR) → [6G in development]
```

Each generation: higher data rates, lower latency, new service types. (Details in Chapter 7.)

---

### 4.3 Key Market Figures — November 2024

| Metric | Value |
|---|---|
| Worldwide cellular subscribers | **8.7 billion** (~106% of world population) |
| LTE (4G) subscribers | **4.99 billion** (57.3% of cellular) |
| 5G subscribers | **2.27 billion** (26% of cellular) |
| Fixed broadband subscribers | **1.59 billion** |
| Mobile broadband subscribers | **7.74 billion** |
| Internet users (individuals) | **5.5 billion** |

---

### 4.4 Cellular Subscribers by Region (per 100 inhabitants)

| Region | Subscribers / 100 inhabitants |
|---|---|
| Europe | 87.2 |
| CIS (Commonwealth of Independent States) | 82.3 |
| The Americas | 81.4 |
| Arab States | 66.3 |
| Asia & Pacific | 60.6 |
| Africa | 32.8 |

> CIS = Russian Commonwealth. Africa shows the largest growth potential.

---

### 4.5 Future Outlook — Ericsson Mobility Report 2025

| Forecast | Value |
|---|---|
| 5G subscriptions end 2025 | ~2.9 billion (1/3 of all mobile) |
| 5G subscriptions by 2031 | **6.4 billion** (2/3 of all mobile) |
| 5G SA subscriptions by 2031 | >4.1 billion (65% of all 5G) |
| 5G overtakes 4G as dominant tech | Expected end of **2027** (9 years after launch) |
| Mobile data traffic growth | +20% between Q3 2024 and Q3 2025 |
| Total mobile data traffic (Q3 2025) | ~157 EB/month |
| 5G coverage (additional people in 2025) | +400 million people |
| 6G commercial launches | Expected first in US, China, Japan, GCC, South Korea |

**6G overview:**
- Standardization: 3GPP Release 21 / ITU IMT-2030
- Architecture: SA-only; builds on 5G SA core
- New capabilities: AI-native, ISAC (Integrated Sensing and Communication)
- New use cases: massive digital twinning, autonomous mobility, wide-area mixed reality
- Projected 6G subscriptions by 2031: ~180 million

---

## 5. Wireless vs Fixed Networks

### 5.1 The 7 Key Differences

| Issue | Wireless | Wired/Fixed |
|---|---|---|
| **Attenuation** | High — signal weakens rapidly over distance | Low — fiber/cable shield and guide the signal |
| **Interference** | No shielding possible; engines, microwaves, other wireless all interfere | Coax/shielded cable physically blocks interference |
| **Spectrum regulations** | Frequencies must be licensed and coordinated; ISM band is overcrowded | No spectrum issue |
| **Data rate** | Lower, though converging (WLAN: hundreds of Mbps; cellular: tens of Mbps) | Higher (Gbps fiber) |
| **Delay & jitter** | High and variable; GSM setup = seconds; other wireless = hundreds of ms | Low and stable |
| **Medium sharing** | Always shared — MAC protocols essential; QoS hard to guarantee | Can be dedicated (point-to-point fiber) |
| **Security** | Radio accessible to anyone; base station can be spoofed/simulated | Physical medium access required |

---

### 5.2 Deeper Explanations

**Attenuation**: Wireless signal strength falls off with the square (or more) of distance in free space, and is further affected by obstacles, reflections, and diffraction. Fiber and cable loss is minimal and predictable.

**Interference**: Electrical motors, fluorescent lighting, microwave ovens, Bluetooth, other Wi-Fi networks — all operate in overlapping frequency ranges and cannot be shielded from radio signals the way coaxial cable shields electrical interference.

**Limited bandwidth**: Although WLAN peak rates have reached 100s of Mbps and 5G has reached Gbps in lab conditions, real user throughput is far lower due to sharing, distance, and propagation effects.

**High delay / jitter**: Large and unpredictable delay variations are particularly harmful for TCP (causes spurious retransmissions) and real-time applications. Wireless protocols must work around this.

**Shared medium**: Radio access is inherently broadcast — every transmission is heard by all devices in range. Medium Access Control (MAC) protocols decide who transmits when, and are a major topic of this course.

**Security**: Any receiver within range can capture wireless frames. A rogue base station (IMSI catcher) can attract mobile phone connections. → Wireless systems must include robust **encryption** and **mutual authentication**.

---

### 5.3 Speed Convergence

Wired and wireless peak speeds are converging over time. However, actual user-perceived throughput is always substantially lower than the theoretical physical-layer maximum, due to:
- Shared medium (capacity divided among users)
- Limited backhaul bandwidth
- Propagation effects
- Interference

---

## 6. Reference Model

### 6.1 Example Architecture

```
[Smartphone]  --radio-->  [Base Station / AP]  --wire-->  [PC]
  Full stack               Interworking unit               Full stack
  App                      (no App/Transport)              App
  Transport                Network                         Transport
  Network                  Data Link                       Network
  Data Link                Physical                        Data Link
  Physical                                                 Physical
      |_________Radio medium_________|_____Wire medium_____|
```

- **End systems** (smartphone, PC): need the **full 5-layer stack**
- **Intermediate systems** (interworking unit/base station): typically only Network + Data Link + Physical
- Higher-layer entities communicate peer-to-peer (App↔App, Transport↔Transport) — the intermediate system is transparent to them

---

### 6.2 Functions of Each Layer (Wireless Context)

| Layer | Wireless-Specific Functions |
|---|---|
| **Physical** | Frequency selection, carrier frequency generation, signal detection, modulation (digital & analog), interference & attenuation handling, encryption |
| **Data Link** | Medium Access Control (MAC), multiplexing of data streams, transmission error correction, frame synchronization |
| **Network** | Addressing, routing, device discovery, **handover** between networks |
| **Transport** | End-to-end connection, congestion & flow control, Quality of Service (QoS) — adapting TCP/UDP for wireless |
| **Application** | Service discovery, multimedia applications, **adaptive applications** (handle large wireless quality variations), wireless WWW access |

---

### 6.3 Layer Details

**Physical layer**: Lowest layer. Converts a bit stream into radio signals (sender) and back (receiver). Must handle: choosing the right frequency, generating a stable carrier, detecting a signal despite interference, and modulating data onto the carrier.

**Data link layer**: Responsible for reliable point-to-point (unicast) or point-to-multipoint (broadcast) links. MAC is the sublayer that decides which device transmits when, preventing collisions on the shared radio medium. Also handles FEC/ARQ for error correction and frame boundary detection.

**Network layer**: Routes packets across multiple intermediate systems (hops). In wireless networks, the critical additional challenge is **handover** — transferring an active connection from one base station/AP to another as the device moves.

**Transport layer**: Provides end-to-end (source-to-destination) reliability. Standard Internet protocol TCP was designed for wired networks with low loss; over wireless, packet loss is misinterpreted as congestion, causing unnecessary rate reductions. Wireless-aware transport protocols are needed.

**Application layer**: Top layer. Must support **adaptive applications** that can function gracefully when bandwidth, delay, and loss rate change dramatically — as they do on a wireless link. Video and interactive gaming are the most demanding (high data rate and low latency, respectively).

---

## 7. Flash Cards

**Q: What is user mobility?**
A: The ability of a user to access the same or similar services at different locations — services "follow" the person.

**Q: What is device portability?**
A: The ability of a communication device to move and maintain connectivity; the network performs handovers to keep it connected.

**Q: Give an example of "fixed + wireless".**
A: WLAN in a historic building where cables cannot be installed; or BWA (Broadband Wireless Access) as a last-mile solution for a new operator.

**Q: What is ABC?**
A: Always Best Connected — devices seamlessly switch between available wireless technologies to always use the best connection quality.

**Q: What is the ISM band?**
A: Industrial, Scientific, and Medical band — unlicensed spectrum (e.g., 2.4 GHz, 5 GHz) used by many wireless devices including Wi-Fi and Bluetooth. Tends to be overcrowded.

**Q: What is ITS?**
A: Intelligent Transportation System — uses V2V, V2I, and V2VRU communication to improve road safety and traffic efficiency.

**Q: What is V2VRU?**
A: Vehicle-to-Vulnerable Road User — direct wireless communication between a vehicle and a pedestrian or cyclist; e.g., warning a truck driver of a cyclist in a blind spot.

**Q: What is the shared medium problem in wireless?**
A: All devices in range share the same radio channel. Without coordination (MAC protocols), multiple simultaneous transmissions collide and are lost. MAC determines who transmits when and is critical for QoS.

**Q: Why is wireless security weaker than wired?**
A: Radio signals are broadcast — any receiver can intercept them. Base stations can be spoofed (IMSI catcher). Encryption and mutual authentication are mandatory.

**Q: What is Industry 5.0 vs. 4.0?**
A: Industry 4.0 is technology-driven (automation, flexibility, productivity). Industry 5.0 is value-driven — it places human-centric and societal goals as the ultimate objectives, with technology as the means.

**Q: What does DAB stand for and what is it used for?**
A: Digital Audio Broadcasting — used in vehicles to receive music, news, weather, and road condition broadcasts.

**Q: Which layers does an interworking unit (base station) typically implement?**
A: Network, Data Link, and Physical layers. It does NOT need Transport or Application layers since those are end-to-end.

**Q: What is FWA?**
A: Fixed Wireless Access — delivering broadband connectivity to homes/offices over a wireless link instead of fiber or cable (e.g., 5G FWA).

**Q: What is CAGR?**
A: Compound Annual Growth Rate — the steady annualized growth rate needed to go from a starting value to an ending value over a period.

**Q: What is Mobile IP?**
A: An extension to the Internet Protocol (IP) that supports user mobility and device portability — allows a device to keep the same IP address while moving between networks.

**Q: What is ISAC?**
A: Integrated Sensing and Communication — a new 6G capability allowing the radio network to simultaneously communicate data AND sense the physical environment (e.g., radar-like detection).

**Q: How many cellular subscribers worldwide as of November 2024?**
A: 8.7 billion — roughly 106% of the world population (some people hold multiple SIMs).

**Q: When is 5G expected to overtake 4G as the dominant mobile access technology?**
A: By the end of 2027, approximately 9 years after the first commercial 5G launches.

---

## 8. Exam Checklist

### Terminology
- [ ] Define user mobility and device portability, and explain the difference
- [ ] Give an example for each of the 4 wireless/mobility combinations (2×2 matrix)
- [ ] Explain why wireless networks must be integrated into fixed networks (3 examples)

### Applications
- [ ] List at least 5 distinct application areas for wireless/mobile networks
- [ ] Explain V2V, V2I, and V2VRU with a concrete example each
- [ ] Explain the ABC paradigm and give a realistic handoff scenario
- [ ] Distinguish between Industry 4.0 and Industry 5.0

### Devices
- [ ] List the device categories from sensor to laptop and describe each
- [ ] State the general performance trade-off for mobile devices

### Market
- [ ] State the key subscriber figures for November 2024 (total, LTE, 5G)
- [ ] Know which region has the lowest and highest cellular penetration
- [ ] Describe the trend in IP traffic split (wired vs Wi-Fi vs cellular, 2017→2022)
- [ ] State when 5G is forecast to overtake 4G, and the 2031 5G subscription forecast
- [ ] Know what 6G is based on and what new capabilities it introduces (ISAC, AI-native)

### Wireless vs Fixed
- [ ] List and explain all 7 key differences between wireless and fixed networks
- [ ] Explain why TCP performs poorly over wireless links
- [ ] Explain the security vulnerabilities specific to wireless networks

### Reference Model
- [ ] Draw the reference model for smartphone ↔ base station ↔ PC
- [ ] State which layers an end system vs. interworking unit needs
- [ ] List the wireless-specific functions of each of the 5 layers
- [ ] Explain what makes the transport layer challenging for wireless

---

*Sources: Moerman, I. (2025). MOBAN Chapter 1 slides. Ericsson Mobility Report, November 2025. Schiller, J. (2003). Mobile Communications, 2nd ed.*
