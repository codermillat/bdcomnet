# Hardware Bill of Materials

All prices in Bangladeshi Taka (৳) based on March 2026 Bangladesh market rates.

---

## LoRa Personal Node (৳2,650 per node)

| Component | Spec | Cost (৳) | Source | Notes |
|---|---|---|---|---|
| TTGO T-Beam v1.1 | ESP32 + SX1276 + GPS + OLED | 1,800 | Robu.in / AliExpress | Must be v1.1 specifically |
| 18650 battery | 3000mAh, flat-top, unprotected | 350 | Robu.in | Samsung 30Q or Sony VTC6 ONLY. Button-top and protected cells are too long — will not fit. |
| USB-C cable | Data-capable (not charge-only) | 100 | Any | Must support data transfer for flashing |
| 2W solar panel | 5V output | 400 | Robu.in / Amazon.in | For continuous outdoor charging |
| **Total** | | **~৳2,650** | | |

### ⚠️ Critical Notes
- **Always attach the LoRa antenna BEFORE powering on.** Powering on without antenna permanently damages the SX1276 radio chip.
- Only flat-top unprotected 18650 cells fit the T-Beam battery holder.
- Order T-Beam v1.1 — other versions have different pinouts.

---

## WiFi Tower (৳67,000 per tower)

| Component | Spec | Cost (৳) | Source |
|---|---|---|---|
| Raspberry Pi 4 (4GB) | Main brain, runs all apps | 5,500 | LKEY BD (official distributor) |
| microSD 64GB | OS + application storage | 700 | Any |
| Ubiquiti Rocket M2 | 2.4GHz, AirMax TDMA, 150Mbps | 9,500 | StarTech BD |
| AirMax Sector 120° antenna × 3 | 2.4GHz, 15dBi, 120° coverage | 18,000 | StarTech BD |
| TP-Link CPE510 × 2 | 5.8GHz backbone, 23dBi, 150Mbps, 15km | 9,000 | StarTech BD |
| 8-port PoE switch | Powers all radios via ethernet | 3,500 | Any |
| 100W solar panel | Monocrystalline | 4,500 | MK Technology BD |
| 100Ah LiFePO4 battery | 12V, cycle life >2000 | 8,000 | Hyper Energy BD |
| MPPT charge controller | 20A, 12V | 2,000 | Any |
| Weatherproof enclosure | IP65 rated | 2,500 | Any |
| Tower pole + mounting | Galvanized steel | 3,000 | Local fabricator |
| CAT6 cable 50m | Outdoor rated | 1,500 | Any |
| Lightning arrester | For antenna protection | 1,500 | Any |
| Misc (connectors, cable ties) | | 1,000 | Any |
| **Total per tower** | | **~৳67,000** | |

### Budget Alternatives

**First prototype (cut cost to ~৳45,000):**
- Replace LiFePO4 100Ah (৳8,000) with lead-acid deep cycle 100Ah (৳4,500) — saves ৳3,500, shorter cycle life
- Replace Rocket M2 + 3 sectors (৳27,500) with single TP-Link CPE210 (৳3,800) for shorter range prototype testing

**Minimum 2-tower system:**
- Tower 1 + Tower 2 = ~৳1,34,000 total
- Covers 15km corridor between two points

---

## Research Testing Kit (₹6,750 from India)

For T-Beam campus testing at Sharda University:

| Component | Quantity | Unit Price (₹) | Total (₹) |
|---|---|---|---|
| TTGO T-Beam v1.1 (with LoRa antenna) | 3 | ~1,800 | ~5,400 |
| 18650 flat-top unprotected battery | 3 | 350 | 1,050 |
| USB-C data cable | 3 | 100 | 300 |
| **Total** | | | **~₹6,750** |

**Order from:** Robu.in (ships within India, reliable)

---

## Where to Buy in Bangladesh

| Store | What They Stock |
|---|---|
| StarTech BD | TP-Link CPE510, Ubiquiti hardware, networking |
| LKEY BD | Raspberry Pi (official distributor) |
| MK Technology BD | Solar panels |
| Hyper Energy BD | LiFePO4 batteries |
| Robu.in | T-Beam, electronics components (ships to BD/India) |
| AliExpress | Cheapest source for T-Beam, 3–4 week shipping |
