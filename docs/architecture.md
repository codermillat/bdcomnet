# BDComNet System Architecture

## Overview

BDComNet uses a two-layer hierarchical mesh architecture. The two layers operate **completely independently** — if all towers fail, the LoRa mesh continues. If LoRa nodes are unavailable, the WiFi towers continue. Dual redundancy is a core design principle.

---

## Layer 1 — LoRa Ground Mesh

### Purpose
Individual-scale resilience. Any person with a LoRa node (~৳2,650, solar-powered) can send text messages, share GPS coordinates, and exchange critical information up to 15km across flat terrain with **no infrastructure dependency whatsoever.**

### Hardware: TTGO T-Beam v1.1
- **MCU:** ESP32 dual-core 240MHz
- **Radio:** Semtech SX1276 LoRa transceiver (433MHz)
- **GPS:** u-blox NEO-6M (3m accuracy, cold start <26s)
- **Display:** 0.96" OLED 128×64
- **Battery:** 18650 Li-ion holder
- **Interface:** USB-C + Bluetooth 4.2

### Firmware: Meshtastic
- Flooding-based mesh routing (configurable hop limit, default: 3)
- AES-256 channel encryption (operator cannot decrypt)
- GPS position broadcast (configurable interval, default: 30s)
- Store-and-forward for offline nodes
- Smartphone via Bluetooth or USB serial

### Radio Configuration (Bangladesh)
| Parameter | Value | Reason |
|---|---|---|
| Region | IN_865 | South Asia compliance |
| Preset | LONG_FAST | 1kbps, 10–20km range |
| Spreading Factor | 11 | Balance of range and speed |
| Bandwidth | 250 kHz | Doubles data rate vs 125kHz |
| Coding Rate | 4/8 | Maximum error correction |
| TX Power | 20 dBm (100mW) | Legal ISM maximum |

### Voice over LoRa — Codec2
| Mode | Bitrate | Quality | Range |
|---|---|---|---|
| Codec2 3200 | 3200 bps | Clear speech | 5–8 km |
| Codec2 1200 | 1200 bps | Acceptable | 10–12 km |
| Codec2 700 | 700 bps | Robotic but usable | 12–15 km |

- Frame size: 40ms → 6 bytes per frame
- Encoding latency (ESP32): 16.8ms
- Decoding latency (ESP32): 15.2ms
- Push-to-talk via Codec2 Talkie Android app over Bluetooth

### GPS and Offline Maps
GPS chip receives signals from GPS (US), GLONASS (Russia), and BeiDou (China) — passive receive only, no internet needed, cannot be tracked.

Location packet broadcast over LoRa (~20 bytes, <1 second):
```json
{
  "node_id": "!ab12cd34",
  "lat": 23.8103,
  "lon": 90.4125,
  "alt": 8,
  "battery": 87,
  "timestamp": "..."
}
```

Offline maps: OrganicMaps app + pre-downloaded Bangladesh map pack (~150MB, downloaded once on WiFi).

---

## Layer 2 — WiFi Tower Backbone

### Purpose
Fixed community towers providing high-throughput smartphone access (2.4GHz, 10–15km radius) interconnected by 5GHz directional links (15–30km tower-to-tower).

### Tower Hardware Stack

```
[Lightning Rod]
      │
[3× AirMax Sector 120° Antennas]   2.4GHz client access, 360° coverage
      │
[Ubiquiti Rocket M2]               Radio 1: client base station
      │
[2× TP-Link CPE510]                Radio 2+3: tower backbone links
      │
[8-port PoE Switch]                Powers all radios via ethernet
      │
[Raspberry Pi 4 (4GB)]             Brain: routing + all applications
      │
[MPPT Solar Charge Controller]
      │
[100W Solar Panel] + [100Ah LiFePO4 Battery]
```

### Radio 1 — Client Access (Phone → Tower)
- **Hardware:** Ubiquiti Rocket M2 + 3× AirMax Sector 120°
- **Frequency:** 2.4GHz ISM (license-free)
- **Protocol:** AirMax TDMA (eliminates collisions, scales to 100+ clients)
- **Throughput:** 150+ Mbps
- **Client range:** 10–15km (flat terrain, elevated antenna)
- **Simultaneous clients:** up to 100 per sector
- **Connection:** Any 2.4GHz WiFi device — no special hardware

### Radio 2 & 3 — Tower Backbone (Tower → Tower)
- **Hardware:** TP-Link CPE510 pairs
- **Frequency:** 5.8GHz ISM (license-free)
- **Antenna gain:** 23dBi directional
- **Throughput:** 150 Mbps
- **Range:** 15–20km per link
- **Protocol:** 802.11n point-to-point bridge

### Tower Brain — Raspberry Pi 4
Running Raspberry Pi OS + LibreMesh:

| Service | Purpose |
|---|---|
| LibreMesh (batman-adv) | Mesh routing between all radios |
| DHCP server | Auto IP assignment to connecting phones |
| DNS resolver | Local domain: bdcomnet.local |
| Matrix / Synapse | Text chat, group channels, voice/video |
| Mumble server | HD voice rooms, push-to-talk channels |
| Nextcloud | File and image sharing |
| Offline map tile server | Pre-loaded Bangladesh OpenStreetMap |

### What Users Access (Any Connected Phone)
| Feature | App | Platform |
|---|---|---|
| Text chat + group messaging | Element | Android, iOS, browser |
| HD voice calls | Mumla (Mumble) | Android, iOS |
| Video calls | Element | Android, iOS |
| File + image sharing | Element + Nextcloud | All |
| Offline GPS map | Pre-loaded tile server | Any browser |
| Push-to-talk voice rooms | Mumla channels | Android, iOS |

### Tower-to-Tower Federation
Mumble and Matrix servers on adjacent towers federate over the 5GHz backbone. A call from Tower A reaches Tower C automatically. From the user's perspective, the entire BDComNet network is one unified platform.

```
Tower A (Dhaka) ←—5GHz—→ Tower B (Comilla) ←—5GHz—→ Tower C (Chittagong)
  Mumble Server              Mumble Server               Mumble Server
  Matrix Server              Matrix Server               Matrix Server
       ↕                          ↕                           ↕
  Local phones              Local phones                Local phones
```

---

## Power System

### Per Tower
| Component | Consumption |
|---|---|
| Rocket M2 + 3 sector antennas | 12W |
| 2× CPE510 backbone | 18W |
| Raspberry Pi 4 | 5W |
| PoE switch | 5W |
| **Total** | **~40W** |

### Solar Sizing
- **Panel:** 100W monocrystalline
- **Daily generation:** 500–600Wh (Bangladesh: 5–6 peak sun hours)
- **Daily consumption:** 40W × 24h = 960Wh
- **Battery:** 100Ah LiFePO4 at 12V = 1,200Wh usable
- **Autonomy without sun:** ~30 hours
- **Extended monsoon option:** 200Ah battery = 2.5 days full autonomy

### Per LoRa Node
- T-Beam in ROUTER mode: ~100mW average
- 18650 3000mAh battery: ~30 hours continuous
- With 2W solar panel: indefinite solar operation

---

## Security and Privacy

| Layer | Measure | Implementation |
|---|---|---|
| LoRa messages | AES-256 channel encryption | Meshtastic built-in |
| WiFi backbone | WPA2 | Standard WiFi |
| Matrix messages | End-to-end encryption | Matrix E2EE (same as Signal) |
| Voice calls | TLS transport | Mumble TLS |
| GPS location | User-controlled precision | Meshtastic position_precision |
| User identity | No phone number required | Node ID only |
| Law enforcement | Court order required | Formal legal process |

Network operators **cannot read message content** — encryption keys exist only on sender and receiver devices. Technically identical to WhatsApp's end-to-end encryption.

---

## Mesh Resilience

### Auto-Failover
```
Normal:       Phone → Tower A → Tower B → Tower C
Tower B fails:
Auto-route:   Phone → Tower A ——————————→ Tower C
(mesh finds alternate path automatically — no configuration)
```

### National Scale
- Bangladesh area: ~147,000 km²
- LoRa node coverage (10km radius, flat terrain): ~314 km²
- Minimum nodes for full coverage: ~470
- Recommended (50% overlap): ~1,000–2,000 nodes
- Cost for national LoRa coverage: ৳15–60 lakh (~$14,000–$55,000)
- Distributed across 10,000 participants: ৳1,500 each

---

## Regulatory Compliance

| Component | Frequency | Status |
|---|---|---|
| LoRa ground mesh | 433 MHz ISM | ✅ License-free, ≤100mW |
| WiFi client access | 2.4 GHz ISM | ✅ BTRC recognized, license-free |
| Tower backbone | 5.8 GHz ISM | ✅ BTRC recognized, license-free |
| TX power | ≤100mW EIRP | ✅ Compliant |

Structured as a private non-commercial community network under the Societies Registration Act 1860 — not a commercial ISP.
