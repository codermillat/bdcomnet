# BDComNet — Bangladesh Disaster Communication Network
### বাংলাদেশ দুর্যোগ যোগাযোগ নেটওয়ার্ক

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status: Research Prototype](https://img.shields.io/badge/Status-Research%20Prototype-blue)]()
[![arXiv](https://img.shields.io/badge/arXiv-coming%20soon-red)]()

> **A solar-powered, community-operated, decentralized communication network that keeps every Bangladeshi connected during cyclones, floods, and other natural disasters — free of charge, forever.**

---

## The Problem

Bangladesh is ranked **24th most vulnerable country** out of 191 in the INFORM Risk Index. During Cyclone Remal and the 2024 monsoon floods, over **18 million people** were affected. Communication breakdown directly caused delays in delivering emergency relief.

When disaster strikes, everything fails simultaneously:
- Cell towers collapse in cyclone winds
- Fiber cables snap in floodwaters
- Power grids go dark across districts
- Families lose contact. People die waiting for help that can't find them.

In July 2024, a single government directive shut down Bangladesh's entire internet for **10 days** — because all traffic flows through just 3 central gateway companies.

**BDComNet eliminates every one of these failure points.**

---

## What BDComNet Is

A two-layer community mesh network built on license-free radio frequencies:

```
LAYER 1 — LoRa Ground Mesh
  Any person with a ৳1,500 node can send texts and GPS location
  up to 15km across flat terrain with no infrastructure at all.

LAYER 2 — WiFi Tower Backbone
  Community towers let any smartphone connect via WiFi (10–15km range)
  for HD voice calls, group messaging, file sharing, and offline maps.
  Towers link to each other up to 30km apart.
```

Both layers are solar powered. Both are end-to-end encrypted. Neither depends on any company, government, or internet connection — ever.

---

## Features

| Feature | LoRa Layer | WiFi Tower Layer |
|---|---|---|
| Text messaging | ✅ | ✅ |
| Voice calls | ✅ Push-to-talk (Codec2) | ✅ HD calls (Mumble) |
| GPS location sharing | ✅ | ✅ |
| Group channels | ✅ | ✅ |
| File & image sharing | ❌ | ✅ |
| Offline maps | ✅ | ✅ |
| Works without internet | ✅ | ✅ |
| Works without electricity | ✅ Solar | ✅ Solar |
| Any smartphone connects | Via Bluetooth | Via WiFi |
| Cost to join | ~৳1,500 (node) | ৳0 (just WiFi) |

---

## System Architecture

```
                    WIFI TOWER BACKBONE
    ┌─────────────────────────────────────────────┐
    │  [Tower A]  ←—5GHz, 150Mbps—→  [Tower B]   │
    │      ↕                               ↕      │
    │  2.4GHz                          2.4GHz     │
    │  10–15km                         10–15km    │
    │      ↕                               ↕      │
    │  [Phones]                        [Phones]   │
    └─────────────────────────────────────────────┘

                    LORA GROUND MESH (everywhere)
    ···[Node]·····[Node]·····[Node]·····[Node]···
       (mesh hops automatically, unlimited range)
```

### Single Tower Hardware Stack
```
[Lightning Rod]
      │
[3× Sector 120° Antennas]  ← 360° phone coverage, 10–15km
      │
[Ubiquiti Rocket M2]        ← Client access base station
      │
[2× TP-Link CPE510]         ← Tower-to-tower backbone links
      │
[8-port PoE Switch]
      │
[Raspberry Pi 4]            ← Brain: routing + all apps
      │
[MPPT Solar Controller]
      │
[100W Solar Panel] + [100Ah LiFePO4 Battery]
```

### Software Stack (All Free, Open Source)

| Software | Purpose |
|---|---|
| LibreMesh / OpenWRT | Mesh routing between towers |
| Matrix / Synapse | Text chat + voice + video calls |
| Mumble | HD voice rooms (push-to-talk) |
| Nextcloud | File and image sharing |
| Meshtastic | LoRa mesh (ground layer) |
| Element | User app for Matrix (Android/iOS) |
| Mumla | User app for Mumble (Android) |
| OrganicMaps | Offline map viewer |

---

## Cost

**Per LoRa Node (personal device):**
| Component | Cost |
|---|---|
| TTGO T-Beam v1.1 | ৳1,800 |
| 18650 battery | ৳350 |
| Small solar panel | ৳500 |
| **Total** | **~৳2,650** |

**Per WiFi Tower (community infrastructure):**
| Component | Cost |
|---|---|
| Raspberry Pi 4 (4GB) | ৳5,500 |
| Ubiquiti Rocket M2 | ৳9,500 |
| 3× Sector antenna | ৳18,000 |
| 2× TP-Link CPE510 | ৳9,000 |
| Solar + battery | ৳14,500 |
| Misc hardware | ৳10,500 |
| **Total per tower** | **~৳67,000** |

---

## Legal Status in Bangladesh

| Frequency | Use | Status |
|---|---|---|
| 433 MHz | LoRa personal nodes | ✅ ISM band, license-free |
| 2.4 GHz | WiFi client access | ✅ BTRC recognized, license-free |
| 5.8 GHz | Tower backbone | ✅ ISM band, license-free |
| Community network | Non-commercial | ✅ Legal |

All frequencies used are in ISM bands recognized by BTRC as license-free for personal and community use.

---

## Quick Start

### Option A — LoRa Node (Personal, ৳2,650)
```bash
# 1. Order hardware (see /hardware/bom.md)
# 2. Flash firmware
#    Open Chrome → https://flasher.meshtastic.org
#    Select: T-Beam v1.1 → Flash

# 3. Configure in Meshtastic app
#    Region: IN_865
#    Preset: LONG_FAST
#    Channel: Join BDComNet public channel or create private group

# 4. Done — attach to solar panel, place on rooftop
```

### Option B — Join a Tower Network
```
1. Look for "BDComNet" WiFi network in your area
2. Connect like any normal WiFi hotspot (no password)
3. Open Element app → connect to your local tower server
4. Start messaging and calling — free forever
```

---

## Repository Structure

```
bdcomnet/
├── README.md                    ← You are here
├── LICENSE                      ← MIT License
├── CITATION.cff                 ← Academic citation
│
├── docs/
│   ├── architecture.md          ← Full two-layer system design
│   ├── hardware-bom.md          ← Parts list with BD prices
│   ├── software-setup.md        ← Install guide (Pi + apps)
│   ├── legal-framework.md       ← Bangladesh law analysis
│   ├── disaster-use-cases.md    ← Cyclone, flood scenarios
│   ├── deployment-guide.md      ← How to build and install
│   └── faq.md                   ← Common questions
│
├── firmware/
│   ├── meshtastic-config.yaml   ← Recommended settings
│   └── custom-scripts/
│
├── hardware/
│   ├── bom.csv                  ← Bill of materials
│   ├── wiring-diagrams/
│   └── solar-calculation.md
│
├── research/
│   ├── paper-draft.md           ← Research paper draft
│   ├── references.bib           ← Citations
│   ├── reading-list.md
│   └── test-data/               ← T-Beam field measurements
│
├── legal/
│   ├── moa-template.md          ← Society registration template
│   ├── terms-of-service.md
│   ├── law-enforcement-policy.md
│   └── btrc-compliance.md
│
└── community/
    ├── guide-bangla.md          ← বাংলা গাইড
    ├── onboarding.md
    └── node-setup.md
```

---

## কীভাবে যোগ দেবেন (How to Join)

বাংলাদেশের যে কেউ এই নেটওয়ার্কে যোগ দিতে পারেন।

**সহজ উপায় (Easy way):**
আপনার এলাকায় BDComNet WiFi টাওয়ার থাকলে সরাসরি যুক্ত হন — কোনো খরচ নেই।

**নিজে নোড বানান (Build your own node):**
মাত্র ৳২,৬৫০ খরচে একটি সোলার-চালিত LoRa নোড তৈরি করুন।
বিস্তারিত: [community/node-setup.md](community/node-setup.md)

**টাওয়ার বানান (Build a tower):**
আপনার এলাকায় একটি WiFi টাওয়ার স্থাপন করুন — বিস্তারিত: [docs/deployment-guide.md](docs/deployment-guide.md)

---

## Research

This project is active academic research. A paper is in preparation:

> **"BDComNet: A Solar-Powered Community Mesh Network Architecture for Disaster-Resilient Communication in Bangladesh"**
> Md Millat Hosen, Dr. Rohit Kumar Sachan, [BUET Co-author]
> Target: NSysS 2026 / Springer Disaster Journal

See [research/paper-draft.md](research/paper-draft.md) for the current draft.

---

## Contributing

All contributions welcome — hardware testing, documentation, translation, deployment.

1. Fork the repo
2. Create a branch: `git checkout -b feature/your-contribution`
3. Commit and push
4. Open a Pull Request

For major contributions, open an issue first to discuss.

---

## Contact

**Lead:** Md Millat Hosen (codermillat)
**Email:** codermillat@gmail.com
**ORCID:** 0009-0005-7198-9893

---

## License

MIT License — free to use, modify, and distribute.
See [LICENSE](LICENSE) for details.

---

*Built by Bangladeshis, for Bangladesh. সবার জন্য, সবসময়।*
*For everyone, always.*
