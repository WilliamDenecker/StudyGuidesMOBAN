# Chapter 2: Wireless Transmission and Medium Access Control
## Comprehensive Study Guide — MOBAN 2025-2026

---

## Table of Contents
1. [Modulation](#1-modulation)
2. [Spread Spectrum](#2-spread-spectrum)
3. [Multiplexing & Multiple Access](#3-multiplexing--multiple-access)
4. [Duplex Techniques](#4-duplex-techniques)
5. [Medium Access Control (MAC)](#5-medium-access-control-mac)
6. [Key Formulas & Calculations](#6-key-formulas--calculations)
7. [Assignment Solutions (Worked Examples)](#7-assignment-solutions-worked-examples)
8. [Flash Cards](#8-flash-cards)
9. [Exam Checklist](#9-exam-checklist)

---

## 1. Modulation

### 1.1 What is Modulation?

The transmission chain for digital data has **two modulation steps**:

```
Digital Data
    ↓  [Digital Modulation]
Analog Baseband Signal
    ↓  [Analog Modulation]
Radio Signal (at carrier frequency fc)
    ↓  [Transmitted over air]
Radio Signal (received)
    ↓  [Analog Demodulation]
Analog Baseband Signal
    ↓  [Synchronization & Decision]
Recovered Digital Data
```

- **Digital modulation**: Converts bits → analog baseband signal (ASK, FSK, PSK, QAM, OFDM)
- **Analog modulation**: Shifts baseband signal up to the radio carrier frequency (AM, FM, PM)

---

### 1.2 Radio Frequency vs. Wavelength

| Frequency | Wavelength | Band | Name |
|---|---|---|---|
| 30–300 Hz | 10⁴–10³ km | ELF | Extremely Low Frequency |
| 300–3000 Hz | 10³–10² km | VF | Voice Frequency |
| 3–30 kHz | 1000–10 km | VLF | Very Low Frequency |
| 30–300 kHz | 10–1 km | LF | Low Frequency |
| 300–3000 kHz | 1–0.1 km | MF | Medium Frequency |
| 3–30 MHz | 100–10 m | HF | High Frequency |
| 30–300 MHz | 10–1 m | VHF | Very High Frequency |
| 300–3000 MHz | 100–10 cm | UHF | Ultra-High Frequency |
| 3–30 GHz | 10–1 cm | SHF | Super-High Frequency |
| 30–300 GHz | 10–1 mm | EHF | Extremely High Frequency (mm-waves) |

**Formula**: λ = c / f, where c = 3×10⁸ m/s

> Higher frequency → shorter wavelength → shorter range → more free-space path loss

---

### 1.3 Signal Propagation Ranges

Three concentric zones around a transmitter:

| Zone | What happens |
|---|---|
| **Transmission range** | Data can be decoded at low error rate → communication possible |
| **Detection range** | Signal is detectable above noise floor, but error rate too high → no communication, PHY header may still be detected |
| **Interference range** | Signal cannot be detected but adds to background noise for others |

> In real life, these zones are **not perfect circles** — obstacles, reflections, and time-varying conditions distort them.

---

### 1.4 Digital Modulation: ASK, FSK, PSK

| Scheme | How it works | Bandwidth | Noise immunity | Use case |
|---|---|---|---|---|
| **ASK** (Amplitude Shift Keying) | Vary amplitude for 0/1 | Low (= baud rate) | Poor (amplitude disturbed by fading) | Rarely used for wireless |
| **FSK** (Frequency Shift Keying) | Different frequencies for 0/1 | Larger (baud rate + carrier separation) | Good | Bluetooth (FHSS) |
| **PSK** (Phase Shift Keying) | Phase shift represents bits | Same as ASK | Better than FSK | WLAN, cellular |

**Why ASK is rarely used over wireless**: Signal amplitude is constantly affected by fading, reflection, and distance. A constant amplitude cannot be guaranteed.

---

### 1.5 Bit Rate vs. Symbol Rate vs. Bandwidth

**Key definitions:**
- **Bit rate** (bps): number of bits transmitted per second
- **Symbol rate / Baud rate** (symbols/s or Baud): number of symbol changes per second
- **Bandwidth** (Hz): frequency range occupied by the signal

**Key relationships:**
```
Bit rate = Symbol rate × bits per symbol
bits per symbol = log₂(number of distinct symbols)

Minimum bandwidth:
  ASK / PSK:  BW_min = Symbol rate (Baud rate)
  FSK:        BW_min = Symbol rate + carrier separation
  OFDM:       Subcarrier spacing = 1 / symbol duration
```

**Example — 4-level ASK (2 bits/symbol):**
- 5 symbols/s → bit rate = 5 × 2 = 10 bps
- BW_min = 5 Hz (around carrier fc)

---

### 1.6 Advanced PSK

| Variant | Bits/symbol | IQ points | Notes |
|---|---|---|---|
| **BPSK** | 1 | 2 (180° apart) | Most robust; used in satellite, poor-SNR links |
| **QPSK** | 2 | 4 (90° apart) | Very common; balance of spectral efficiency and robustness |
| **DQPSK** | 2 | 4 | Differential variant — no reference signal needed at receiver |
| **8-PSK** | 3 | 8 | More bits/symbol but needs better SNR |

- QPSK uses **IQ representation**: I axis (in-phase, 0°), Q axis (quadrature, 90°)
- Higher SNR → more phase points → more bits per symbol
- When SNR drops, constellations blur and bit errors rise

---

### 1.7 Quadrature Amplitude Modulation (QAM)

- Combines **amplitude AND phase** variation
- **n bits/symbol → 2ⁿ discrete constellation points**
- n = 2 is identical to QPSK

| Modulation | Bits/symbol | Constellation points | SNR requirement |
|---|---|---|---|
| BPSK | 1 | 2 | Lowest |
| QPSK | 2 | 4 | Low |
| 16-QAM | 4 | 16 | Medium |
| 64-QAM | 6 | 64 | High |
| 256-QAM | 8 | 256 | Very High |
| 1024-QAM | 10 | 1024 | Extreme |

**Used in**: IEEE 802.11 (WLAN), LTE/5G (adaptive modulation — switch QAM order based on channel quality)

**EVM (Error Vector Magnitude)**: Measures how accurately symbols are placed in the constellation. More negative (in dB) = more accurate.

---

### 1.8 Hierarchical Modulation

- Embeds a high-priority (HP) stream inside a lower-priority (LP) stream
- **Example (DVB-T)**: 64-QAM contains an embedded QPSK
  - 2 most significant bits = QPSK (HP — standard resolution TV)
  - Remaining 4 bits = additional QAM levels (LP — high resolution)
  - Under poor reception: receiver decodes only QPSK → still gets standard TV

---

### 1.9 OFDM (Orthogonal Frequency Division Multiplexing)

**Concept**: Split a high-rate data stream into many parallel low-rate streams, each on its own subcarrier.

```
High bit-rate stream
    ↓
Split into n substreams
    ↓
Each substream modulates its own subcarrier (at different frequency)
    ↓
All subcarriers transmitted simultaneously
```

**Key properties:**
- Subcarriers are **orthogonal** — the peak of one subcarrier aligns with the zero crossings of all others → no inter-carrier interference
- Implemented efficiently using **FFT/IFFT**
- **Longer symbol duration** → less sensitive to Inter-Symbol Interference (ISI)
- **Frequency-selective fading** only affects some subcarriers, not the whole signal

**Used in**: IEEE 802.11a/g/n/ac/ax (Wi-Fi), LTE, 5G NR, DAB

**Subcarrier spacing formula**: Δf = 1 / T_symbol

**Example (IEEE 802.11a/g)**: 48 data subcarriers + 4 pilot subcarriers

---

### 1.10 Pulse Shaping & Bandwidth

- **Narrow pulses (short symbol duration T)** → wide bandwidth (BW ≈ 1/T)
- **Wide pulses (long symbol duration T)** → narrow bandwidth
- **Pulse shaping** (Raised Cosine filter): tradeoff between time-domain spreading and frequency containment
- ISI occurs when symbol duration < delay spread of the channel

---

### 1.11 IQ Representation

- All RF modulation can be described in the **IQ plane** (complex plane)
- **I (In-phase)**: projection on x-axis (0° carrier)
- **Q (Quadrature)**: projection on y-axis (90° carrier)
- Each modulation scheme creates a characteristic **constellation diagram** (scatter plot)
- At the receiver, the scatter plot blurs with decreasing SNR until symbols can no longer be separated

---

## 2. Spread Spectrum

### 2.1 Why Spread Spectrum?

**Problem**: Narrowband signals can be completely wiped out by narrowband interference or frequency-selective fading.

**Solution**: Spread the narrowband signal over a much wider bandwidth using a special code.

```
Narrow signal → [Spreading with code] → Wide broadband signal → [Transmit]
                                                                      ↓
                                                              Receiver
Wide signal + interference → [Despreading with same code] → Narrow signal recovered
                                                           + interference spread out
                                                           → [Bandpass filter] → Clean signal
```

### 2.2 Advantages & Disadvantages of Spread Spectrum

| Advantages | Disadvantages |
|---|---|
| Protection against narrowband interference | Requires larger frequency band |
| Coexistence of multiple signals without coordination | Increased receiver complexity |
| Security / tap-proof (signal looks like noise) | Raises background noise level for others |
| Resistance to frequency-selective fading | (Requires precise power control for CDMA) |

---

### 2.3 DSSS — Direct Sequence Spread Spectrum

**Mechanism**: XOR the data bit stream with a high-speed pseudo-random **chipping sequence**

```
User bit:       0  (duration = tb)
Chipping seq:   0 1 1 0 1 0 1  (each chip has duration tc)
Result (XOR):   0 1 1 0 1 0 1  (complement for bit=1)
```

**Key parameters:**
- **Spreading factor** s = tb / tc (number of chips per bit)
- **Bandwidth after spreading** = s × original bandwidth
- Civil applications: s = 10–100; Military: up to 10,000
- **Barker code** (11-chip: 10110111000) used in IEEE 802.11 DSSS

**Advantages of DSSS:**
- Reduces frequency-selective fading
- In cellular: adjacent base stations can use the **same frequency** (reuse = 1)
- **Soft handover**: multiple base stations can receive and combine the same signal

**Disadvantages:**
- Requires **precise power control** (near-far problem)
- Complex receiver (correlator, integrator, decision unit)

**DSSS Receiver operation:**
1. Demodulate carrier → spread baseband signal
2. Multiply by synchronized chipping sequence (correlator)
3. Integrate over one bit period
4. Decision unit: if sum > threshold → bit = 1, else bit = 0

---

### 2.4 FHSS — Frequency Hopping Spread Spectrum

**Mechanism**: Transmitter hops between frequencies according to a pseudo-random **hopping sequence**

| Version | Definition | Properties |
|---|---|---|
| **Slow hopping** | Multiple bits per frequency hop (dwell time > bit period) | Simpler, cheaper, less robust |
| **Fast hopping** | Multiple hops per bit (dwell time < bit period) | More complex, much more robust to narrowband interference |

**Parameters:**
- **Dwell time** (td): time spent on each frequency
- **Hopping sequence**: pseudo-random order of frequencies used

**Examples:**
- **Bluetooth**: 1600 hops/s, 79 channels, 1 MHz spacing, 2.4 GHz ISM band → slow hopping FHSS
- **GSM**: optional slow frequency hopping

**Advantages vs DSSS:**
- Simpler implementation
- Uses only a small portion of spectrum at any time

**Disadvantages vs DSSS:**
- Less robust to fading and multipath

---

### 2.5 DSSS vs FHSS Comparison

| Feature | DSSS | FHSS |
|---|---|---|
| Mechanism | XOR with chipping sequence | Hop between frequencies |
| Interference resistance | Very high (spreads interference) | High (only short dwell time per frequency) |
| Complexity | High | Lower |
| Multipath robustness | Higher | Lower |
| Uses full bandwidth | Always | No (only one sub-band at a time) |
| Example | IEEE 802.11 (legacy), UMTS/CDMA | Bluetooth, GSM slow hopping |

---

## 3. Multiplexing & Multiple Access

### 3.1 Key Distinction

| Term | Meaning |
|---|---|
| **Multiplexing** | Technical mechanism for sharing the medium (physical layer) |
| **Multiple Access** | Control/assignment protocol deciding who gets what resource |

**Four dimensions for multiplexing:**

| Dimension | Multiplexing | Multiple Access |
|---|---|---|
| Space (s) | SDM — Space Division Multiplexing | SDMA |
| Frequency (f) | FDM — Frequency Division Multiplexing | FDMA |
| Time (t) | TDM — Time Division Multiplexing | TDMA |
| Code (c) | CDM — Code Division Multiplexing | CDMA |

> **Guard spaces are always needed** between channels (in time, frequency, space, or code) to avoid interference.

---

### 3.2 SDM / SDMA — Space Division

**Concept**: Separate channels by geographic separation. Same frequency can be reused in non-overlapping areas.

**Key insight**: Signal attenuation ∝ d⁻² to d⁻⁴ → at sufficient distance, same frequency can be reused.

**Cellular network example:**
- Cells are grouped into **clusters** (3-cell, 7-cell, etc.)
- No two adjacent cells use the same frequency
- **Cell sectoring**: split one cell into 3 sectors using directional antennas → triple the frequency reuse

| Cluster size | Frequency reuse | Co-channel interference |
|---|---|---|
| 3-cell | High (aggressive reuse) | More |
| 7-cell | Lower | Less |

---

### 3.3 FDM / FDMA — Frequency Division

**Concept**: Divide the total spectrum into non-overlapping frequency bands. Each channel uses its band continuously.

```
f: [—guard—|—channel 1—|—guard—|—channel 2—|—guard—|—channel 3—|—guard—]
t: ←────────────────── continuous ──────────────────────────────────────→
```

**Advantages:**
- No dynamic coordination needed
- Works for both analog and digital signals
- Simple receivers (just tune to the right frequency)

**Disadvantages:**
- Wasteful when traffic is bursty (frequency reserved even when idle)
- Inflexible — fixed assignment
- Guard bands waste capacity

**FDMA example (cellular)**: Each mobile user gets a dedicated uplink and downlink frequency pair.

---

### 3.4 TDM / TDMA — Time Division

**Concept**: All channels share the full spectrum, but each uses it during a different time slot.

```
f: ←─────────────── full spectrum ───────────────────→
t: [guard|slot 1|guard|slot 2|guard|slot 3|guard|slot 4|...]
         ↑ user A          ↑ user B
```

**Advantages:**
- Only one carrier at a time → no adjacent carrier interference
- Flexible: assign more slots to heavy users
- High throughput even with many users

**Disadvantages:**
- Requires **precise synchronization** between all senders
- Guard times waste capacity

**TDMA in practice**: GSM uses FDMA + TDMA (8 time slots per frequency channel)

---

### 3.5 CDM / CDMA — Code Division

**Concept**: All channels use the **same frequency at the same time**, separated by unique orthogonal codes.

```
f: ←─── same band ───→
t: ←─── same time ───→
c: [code A] [code B] [code C] ...  ← ALL SIMULTANEOUS
```

**Implementation**: Via spread spectrum (DSSS)
- Each user's data XORed with a unique chipping code
- At receiver: multiply received signal by known code → integrator → decision
- Orthogonal codes ensure no cross-user interference when all signals have equal power

**Advantages:**
- Excellent interference protection and tap-proof security
- Bandwidth efficient (all users share same band simultaneously)
- Soft handover in cellular networks

**Disadvantages:**
- **Near-far problem**: nearby strong signals drown out distant weak signals → precise power control is essential
- Complex receiver
- Lower per-user data rate (due to spreading)

**Party analogy**: CDM is like people speaking different languages in the same room. If you know the language (code), you understand the conversation; others hear it as noise.

---

### 3.6 Time + Frequency Multiplexing Combined

**Concept**: Each channel gets a specific frequency band for a specific time duration.

```
f: [f1][f2][f3]...
t:  1   2   3  ...
```

**Advantages over pure CDM**: Higher per-user data rates (no spreading overhead)
**Examples**: GSM (FDMA/TDMA/FDD), Bluetooth (FDMA/TDMA + FHSS)

---

### 3.7 Multiplexing Comparison Table

| Method | Channel = | Advantages | Disadvantages | Examples |
|---|---|---|---|---|
| **FDMA** | Frequency band | Simple, no sync needed | Wasteful for bursty traffic, inflexible | GSM (combined with TDMA), FM radio |
| **TDMA** | Time slot | Flexible, high efficiency | Needs precise sync | GSM, DECT |
| **CDMA** | Code | Secure, efficient, soft handover | Near-far problem, complex, power control | UMTS (3G), CDMA2000 |
| **SDMA** | Space/cell | Reuse spectrum geographically | Requires careful planning | All cellular systems |
| **OFDMA** | Frequency + Time subcarrier | Very flexible, robust to ISI | Complex synchronization | LTE, 5G NR, Wi-Fi 6 |

---

## 4. Duplex Techniques

### 4.1 Why Duplex?

Mobile communication is **bidirectional** (uplink: device→base, downlink: base→device). Two methods to support this:

| Method | Mechanism | Notes |
|---|---|---|
| **FDD** (Frequency Division Duplex) | Separate frequency bands for UL and DL | Simultaneous TX and RX possible; needs frequency guard band; e.g., GSM: 45 MHz separation |
| **TDD** (Time Division Duplex) | Alternate time slots for UL and DL on one frequency | Flexible UL/DL split; simpler hardware; but TX and RX cannot happen simultaneously |

### 4.2 Half-duplex vs Full-duplex

| Mode | Behavior | Examples |
|---|---|---|
| **Half-duplex** | Either transmit OR receive, not both simultaneously | Walkie-talkie, Wi-Fi (per channel), single-channel technologies |
| **Full-duplex** | Transmit AND receive simultaneously | Mobile phone with FDD, multi-channel technologies |

> **Wi-Fi (802.11)** is half-duplex per channel — it can send OR receive but not both at the same time on the same channel. Wi-Fi 6 with multiple antennas enables effective full-duplex capability.

### 4.3 Common Combinations

| Combination | Description | Example |
|---|---|---|
| FDMA/FDD | Each user has own UL+DL frequency pair | GSM (combined with TDMA) |
| FDMA/TDD | One frequency, UL and DL in alternating slots | DECT |
| FDMA/FDD/TDMA | Multiple users per frequency via time slots, FDD | GSM |
| FDMA/TDD/TDMA | Same but TDD | DECT |

---

## 5. Medium Access Control (MAC)

### 5.1 Why Wireless Needs Special MAC

**Can we reuse CSMA/CD from Ethernet (IEEE 802.3)?** → **NO**

| CSMA/CD assumption | Why it fails wirelessly |
|---|---|
| Signal strength uniform across wire | Strength ∝ d⁻² to d⁻⁴ → colissions at receiver ≠ sender |
| Carrier sense detects all transmissions | **Hidden terminal problem**: sender can't sense a competing sender |
| Collision detection reliable | TX power >> RX power → sender can't hear collision at receiver |

**Three core MAC problems in wireless:**

---

### 5.2 Hidden Terminal Problem

```
         range of A           range of C
    ←──────────────→     ←──────────────→
    A ────────────────── B ────────────────── C
```

1. **A senses channel** → idle → starts transmitting to B
2. **C senses channel** → can't hear A → thinks it's idle → starts transmitting to B
3. **Collision at B** — but neither A nor C detects it
4. **Result**: A is "hidden" from C and vice versa → wasted bandwidth, reduced throughput

---

### 5.3 Exposed Terminal Problem

```
    A ─── B ─── C ─── D
```

1. B is sending to A
2. C wants to send to D (which is outside A's and B's range)
3. C senses carrier → detects B's signal → defers unnecessarily
4. C is "exposed" to B → wasted opportunity, reduced throughput

---

### 5.4 Near-Far Problem

```
    A ─────────────────────── C ──── B ──── C
```

- A and B both transmit to C at the same power
- B is much closer to C → B's signal drowns out A's signal at C
- **Critical for CDMA**: all signals must arrive at receiver with similar power
- **Solution**: precise power control (e.g., UMTS adjusts power 1500 times/second)

---

### 5.5 ALOHA

**Context**: Use TDM without central coordination — packet-based random access.

| Variant | Mechanism | Max efficiency |
|---|---|---|
| **Basic ALOHA** | Send whenever you have data; retransmit after random backoff on collision | **18%** |
| **Slotted ALOHA** | Same but transmissions must start at slot boundaries (needs synchronization) | **36%** |

**How it works:**
1. If you have data → send it
2. If collision detected → wait a random time → retransmit
3. Repeat until successful or max retries

**Properties:**
- No central coordination
- Works well under **light load**; breaks down under heavy load
- Cannot guarantee maximum delay or minimum throughput
- Still used in 3G/4G/5G for **initial random access** (random access channel)

---

### 5.6 MACA — Multiple Access with Collision Avoidance

**Goal**: Solve hidden terminal problem without a base station, using short signaling frames.

**Two control packets:**
- **RTS (Request To Send)**: Sender asks receiver for permission; contains sender address, receiver address, packet size/duration
- **CTS (Clear To Send)**: Receiver grants permission; contains same info

**Procedure (solving hidden terminal):**

```
A ────────────────── B ────────────────── C
      (A → B)                    (C → B: wants to send)

Step 1: A sends RTS to B (C cannot hear this)
Step 2: B sends CTS to A (C CAN hear this)
Step 3: C hears CTS → knows medium is reserved → defers for stated duration
Step 4: A transmits data to B → no collision
```

**MACA also solves exposed terminal:**
- B sends RTS to A
- C hears RTS, is NOT the destination → does not react
- A sends CTS (C cannot receive it — A is out of C's range)
- C correctly infers it can transmit (it missed CTS → A is outside range → no collision possible there)

**Limitation:**
- RTS/CTS collisions can still occur (but short packets → low probability)
- RTS/CTS overhead is not negligible for small data packets
- MACA assumes symmetrical transmission conditions

**IEEE 802.11 usage**: MACA-style RTS/CTS is an optional mechanism in Wi-Fi (DCF mode).

---

### 5.7 MAC Scheme Summary

| Scheme | Type | Solves | Limitations |
|---|---|---|---|
| **CSMA/CD** | Contention (wired) | — | Fails wirelessly |
| **Basic ALOHA** | Random access | No coordination needed | 18% efficiency, no guarantees |
| **Slotted ALOHA** | Random access | Better than basic | 36% efficiency, needs sync |
| **MACA (RTS/CTS)** | Random + reservation | Hidden & exposed terminal | RTS/CTS overhead |
| **TDMA (fixed)** | Scheduled | Guaranteed slots | Wasteful if slot unused |
| **FDMA** | Scheduled | No interference | Wasteful for bursty |
| **CDMA** | Spread-spectrum | Simultaneous access | Near-far, power control |

---

## 6. Key Formulas & Calculations

### Modulation Formulas

| Formula | Description |
|---|---|
| **Bit rate = Symbol rate × log₂(M)** | M = number of distinct signal levels/symbols |
| **Symbol rate = Bit rate / log₂(M)** | Inverse of above |
| **BW_min (ASK/PSK) = Symbol rate** | Minimum bandwidth equals baud rate |
| **BW_min (FSK) = Symbol rate + f_separation** | Add carrier separation |
| **BW_min (FSK half-duplex) = Symbol rate + f_sep** | Single band |
| **BW_min (FSK full-duplex) = (Symbol rate + f_sep) / 2 per dir** | Two equal halves |
| **OFDM subcarrier spacing Δf = 1 / T_symbol** | Orthogonality condition |
| **OFDM bit rate = n_subcarriers × symbol_rate × bits_per_symbol** | Total OFDM throughput |
| **λ = c / f** | Wavelength from frequency (c = 3×10⁸ m/s) |
| **Spreading factor s = t_b / t_c** | DSSS: bit period / chip period |

### Duplex / Bandwidth Formulas

| Scenario | Bandwidth |
|---|---|
| Half-duplex | Full bandwidth used in one direction at a time |
| Full-duplex FDD | Total BW split equally: BW/2 per direction; carrier = center of each half |

---

## 7. Assignment Solutions (Worked Examples)

### Q1 — Symbol rate and bit rate from signal units
> An analog signal carries **6 bits per signal unit**. **1000 signal units/second** are sent. Find the symbol rate and the ASK bit rate.

- Symbol rate = **1000 symbols/s** (= 1000 signal units/s)
- Bit rate = Symbol rate × bits/symbol = 1000 × 6 = **6000 bps = 6 kbps**

---

### Q2 — Symbol rate from bit rate and bits/symbol
> **Bit rate = 4 kbps**, each signal unit carries **8 bits**. Find the symbol rate.

- Symbol rate = Bit rate / bits per symbol = 4000 / 8 = **500 symbols/s**

---

### Q3 — Minimum bandwidth for multi-level ASK (half-duplex)
> Find minimum bandwidth for a **4-level ASK** signal at **20 Mbps**, half-duplex.

- 4 levels → log₂(4) = **2 bits/symbol**
- Symbol rate = 20 Mbps / 2 = **10 M symbols/s**
- Minimum bandwidth = Symbol rate = **10 MHz**
- (Half-duplex: only one direction at a time → full 10 MHz used for that direction)

---

### Q4 — Full-duplex FDD bandwidth and carrier frequencies
> Bandwidth = **10 MHz** (2.400 GHz to 2.410 GHz), full-duplex ASK. No gap between bands. Find carriers and bandwidth per direction.

- Total BW = 10 MHz, split equally → **5 MHz per direction**
- Direction 1: 2.400–2.405 GHz → carrier = 2.4025 GHz
- Direction 2: 2.405–2.410 GHz → carrier = 2.4075 GHz
- **Carrier frequencies: 2.4025 GHz and 2.4075 GHz**

---

### Q5 — Minimum bandwidth for FSK (half-duplex)
> FSK signal at **2 Mbps**, half-duplex, carrier separation = **3 MHz**. Find spectral bandwidth.

- Symbol rate = 2 Mbps (1 bit/symbol for FSK)
- BW_min (FSK) = Symbol rate + carrier separation = 2 + 3 = **5 MHz**

---

### Q6 — Maximum bit rate for FSK (full-duplex)
> Medium bandwidth = **12 MHz**, difference between carriers = **2 MHz**, full-duplex FSK.

- Full-duplex → BW per direction = 12 / 2 = **6 MHz**
- BW_FSK = Symbol rate + carrier separation
- 6 = Symbol rate + 2 → Symbol rate = **4 M symbols/s**
- FSK (binary): 1 bit/symbol → **Bit rate = 4 Mbps per direction**

---

### Q7 — Symbol rate and bit rate for 8-PSK
> Bandwidth = **50 MHz**, 8-PSK signal.

- 8-PSK → log₂(8) = **3 bits/symbol**
- BW = Symbol rate → Symbol rate = **50 M symbols/s**
- Bit rate = 50 M × 3 = **150 Mbps**

---

### Q8 — Bit rate for 32-QAM
> 32-QAM, symbol rate = **100 M symbols/s**.

- 32-QAM → log₂(32) = **5 bits/symbol**
- Bit rate = 100 M × 5 = **500 Mbps**

---

### Q9 — OFDM bit rate and subcarrier spacing
> 64-QAM OFDM, **50 subcarriers**, symbol duration = **4 µs**.

- 64-QAM → log₂(64) = **6 bits/symbol**
- Symbol rate per subcarrier = 1 / 4 µs = **250 k symbols/s**
- Bit rate = 50 × 250k × 6 = 50 × 1.5 Mbps = **75 Mbps**
- Minimum subcarrier spacing Δf = 1 / T_symbol = 1 / 4 µs = **250 kHz**

---

## 8. Flash Cards

**Q: What are the two modulation steps in a radio transmitter?**
A: (1) Digital modulation: converts bits to analog baseband signal. (2) Analog modulation: shifts baseband signal up to radio carrier frequency.

**Q: Why is ASK rarely used for wireless transmission?**
A: Signal amplitude is constantly disturbed by fading, reflections, and distance variations — a constant amplitude cannot be guaranteed wirelessly.

**Q: What is the minimum bandwidth of an ASK signal?**
A: Equal to the symbol rate (baud rate). BW_min = N_Baud.

**Q: How does OFDM reduce Inter-Symbol Interference?**
A: By splitting the high-rate stream into many low-rate parallel streams on different subcarriers, each with a much longer symbol duration. This makes the symbol period much longer than the channel's delay spread.

**Q: What is the subcarrier spacing in OFDM?**
A: Δf = 1 / T_symbol (orthogonality condition).

**Q: What is the spreading factor in DSSS?**
A: s = t_b / t_c (bit period / chip period). Bandwidth after spreading = s × original bandwidth.

**Q: What is the key advantage of DSSS over FHSS?**
A: DSSS is more robust against multipath fading and offers soft handover capability in cellular networks.

**Q: What is slow vs. fast hopping in FHSS?**
A: Slow hopping: multiple bits per frequency hop. Fast hopping: multiple frequency hops per bit (more robust, more complex).

**Q: What are the 4 multiplexing dimensions?**
A: Space (SDM), Frequency (FDM), Time (TDM), Code (CDM).

**Q: What is the difference between multiplexing and multiple access?**
A: Multiplexing is the physical mechanism for sharing the medium. Multiple access is the control protocol that assigns resources (time/frequency/code/space) to users.

**Q: Why does CSMA/CD fail in wireless networks?**
A: (1) Signal power decreases with distance so collisions at the receiver may not be detectable at the sender. (2) Hidden terminal problem: a sender may not be able to hear competing senders.

**Q: Describe the hidden terminal problem.**
A: Node A and C are both out of each other's range but both in range of B. A is transmitting to B; C cannot detect A's transmission, senses idle channel, transmits to B → collision at B that neither A nor C can detect.

**Q: Describe the exposed terminal problem.**
A: C can hear B transmitting to A, so C defers from transmitting to D. But C's transmission to D would not have caused a collision at A (A is out of C's range). C is unnecessarily blocked — wasted capacity.

**Q: Describe the near-far problem.**
A: A strong nearby transmitter drowns out a weaker far transmitter at the receiver. Critical for CDMA: without power control, the nearest device monopolizes the channel. Solution: closed-loop power control.

**Q: What is the maximum efficiency of Basic ALOHA and Slotted ALOHA?**
A: Basic ALOHA: 18%. Slotted ALOHA: 36% (double, because slotting halves the collision window).

**Q: How does MACA solve the hidden terminal problem?**
A: Sender A sends a short RTS to receiver B. B responds with CTS which is heard by hidden node C. C defers for the duration indicated in the CTS, preventing it from transmitting during A's data exchange with B.

**Q: What is FDD vs TDD?**
A: FDD (Frequency Division Duplex): separate frequency bands for uplink and downlink — simultaneous TX/RX possible. TDD (Time Division Duplex): same frequency, alternating time slots for UL and DL — cannot TX and RX simultaneously.

**Q: What is EVM?**
A: Error Vector Magnitude — a measure of how accurately a transmitter places symbols on the constellation diagram. More negative dB = more accurate (better quality transmitter).

**Q: What does hierarchical modulation do?**
A: Embeds a high-priority (HP) signal in the most significant bits of a higher-order QAM, and low-priority (LP) data in the remaining bits. Poor-reception receivers can still decode the HP stream (e.g., standard TV resolution) even if they can't decode the full QAM.

**Q: Which modulation scheme is most robust, and why?**
A: BPSK — only 2 constellation points 180° apart, maximum distance between points → highest noise tolerance. Used in satellite links, deep coverage scenarios.

**Q: What is the difference between CDM and CDMA?**
A: CDM (Code Division Multiplexing) is the mechanism (all users share same frequency/time, separated by code). CDMA (Code Division Multiple Access) is the access control protocol that assigns codes to users.

---

## 9. Exam Checklist

### Modulation
- [ ] Draw and explain the full TX/RX chain (digital modulation → analog modulation → transmission → demodulation → decision)
- [ ] Name and compare the 3 basic digital modulation schemes (ASK, FSK, PSK) on: bandwidth, noise immunity, complexity
- [ ] Calculate bit rate, symbol rate, and minimum bandwidth for ASK, FSK, PSK, QAM
- [ ] Explain QPSK using the IQ diagram (4 points, 2 bits/symbol)
- [ ] Explain QAM: formula for bits/symbol, constellation size, when to use higher QAM order
- [ ] Explain OFDM: concept, ISI resistance, subcarrier spacing formula, use cases
- [ ] Explain hierarchical modulation with DVB-T example
- [ ] Explain the pulse shaping tradeoff (time domain vs. frequency domain)
- [ ] Know the RF frequency bands and the wavelength formula

### Spread Spectrum
- [ ] Explain why spread spectrum is needed (frequency-selective fading, narrowband interference)
- [ ] Draw the 5-step spread/despread process (power spectral density diagram)
- [ ] Explain DSSS: XOR with chipping sequence, spreading factor formula, advantages (soft handover, frequency reuse = 1)
- [ ] Explain FHSS: slow vs. fast hopping, dwell time, Bluetooth as example
- [ ] Compare DSSS and FHSS on: robustness, complexity, bandwidth usage

### Multiplexing & Multiple Access
- [ ] Name and describe all 4 multiplexing dimensions and their multiple access counterparts
- [ ] Explain SDM/SDMA and cell clustering (3-cell and 7-cell clusters)
- [ ] Explain FDM/FDMA: pros/cons, when wasteful
- [ ] Explain TDM/TDMA: pros/cons, synchronization requirement
- [ ] Explain CDM/CDMA: orthogonal codes, power control, near-far problem
- [ ] Explain OFDMA as a combination used in LTE/5G

### Duplex
- [ ] Distinguish FDD vs TDD — when is each preferred?
- [ ] Distinguish half-duplex vs full-duplex — example of each
- [ ] Identify the duplex scheme used in GSM (FDD), DECT (TDD)

### MAC
- [ ] Explain why CSMA/CD fails in wireless networks (3 reasons)
- [ ] Describe the hidden terminal problem with a diagram
- [ ] Describe the exposed terminal problem with a diagram
- [ ] Describe the near-far problem and its solution
- [ ] Explain Basic ALOHA and Slotted ALOHA — efficiency figures and assumptions
- [ ] Explain MACA: RTS/CTS exchange, how it solves hidden terminal, how it solves exposed terminal
- [ ] State the overhead/limitation of MACA

### Calculations
- [ ] Solve symbol rate / bit rate / bandwidth problems for ASK, FSK, PSK, QAM
- [ ] Solve OFDM bit rate and subcarrier spacing problems
- [ ] Apply full-duplex vs half-duplex to bandwidth/carrier calculations

---

*Sources: Moerman, I. (2025). MOBAN Chapter 2 slides. Schiller, J. (2003). Mobile Communications, 2nd ed. Popovski, P. (2020). Wireless Connectivity.*
