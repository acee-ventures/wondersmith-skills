---
name: bike-accessory-90usd
description: Production recipe for bike-mounted electronics targeting ~$50-120 retail — front/rear lights, GPS beacons, cadence sensors, handlebar controllers. Weather-sealed IPX7, USB-C rechargeable LiPo, BLE-optional, high-vibration-tolerant PCB with conformal coat. Use when the prompt describes a device that mounts on a bicycle frame, handlebars, seat post, or wheel hub, retail $30-$130.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: bike-accessory
  budgetBand:
    bomMin: 14
    bomMax: 32
    retailTarget: 90
  manufacturingDays: 7
  componentCount: 13
  coreComponents:
    - "nRF52810 (BLE) or ATtiny1616 (non-BLE) based on feature set"
    - "LiPo pouch 500-1000mAh with PTC thermal fuse (cycling vibration)"
    - "TP4056 + DW01 protection; USB-C behind silicone flap for IPX7"
    - "High-CRI Cree XP-G3 LED if light product (front beam ≥200 lumens)"
    - "Conformal coated PCB (acrylic or urethane) — non-negotiable for outdoor"
    - "Silicone strap mount (no threaded zip-ties — UV degradation <1yr)"
    - "Accelerometer for cadence/motion: LIS3DH at 10Hz sampling"
  safetyClass: outdoor-high-vibration
  complianceMust:
    - "IPX7 weatherproof: 30min submersion at 1m (verified via hydrostatic pressure test)"
    - "Vibration tolerance: IEC 60068-2-6 class 5 (10-500Hz, 2g for 1hr/axis)"
    - "StVZO certification for DE market if it's a headlight (specific beam shape)"
    - "FCC Part 15 class B if BLE"
    - "RoHS 3 / REACH SVHC"
  sourceRun: null
---

# Bike Accessory $90 Recipe

## Must-haves for durability

- **Conformal coat PCB** — assume rain + salt + bike-wash soap will reach the electronics.
- **Vibration strain relief** on all wires — solder joints fail first under 5000km of cobblestones.
- **Silicone USB-C flap**, not a metal cap — metal cap threads strip; silicone lasts.
- **Battery thermal fuse** — LiPo + road vibration = small but real physical shock risk.

## Mount geometry

Bars: Ø22.2mm (road drop bars), Ø31.8mm (MTB/gravel), Ø35mm (some race road). Design a silicone strap that fits 22-35mm range with replaceable tension plate.

## Cost sketch (50 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| MCU + passives | 50 | $1.60 | $80 |
| LiPo + protection | 50 | $2.80 | $140 |
| LED + driver (if light) | 50 | $3.40 | $170 |
| Accelerometer | 50 | $1.20 | $60 |
| Conformal coat (service fee) | 50 | $0.90 | $45 |
| Injection-molded PC shell | 50 | $3.60 | $180 |
| Silicone mount + flap | 50 | $2.20 | $110 |
| Assembly + IPX7 verify | 50 | $2.80 | $140 |
| **Total** | | | **~$925** |
| Per-unit landed | | | **~$18** |
