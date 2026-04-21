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

<!-- cadquery-base -->
```python
import cadquery as cq
import math

# ── Handlebar-mounted pod ───────────────────────────────────────────────────
# Weather-sealed body with a split silicone strap channel for Ø22.2-35mm bars.
# USB-C port behind a recessed silicone flap on the rear.
POD_L   = 60.0   # along handlebar
POD_W   = 26.0
POD_H   = 22.0
CORNER  = 4.0
WALL    = 2.0
BAR_MIN = 22.2
BAR_MAX = 35.0
STRAP_W = 12.0   # silicone strap channel width
USBC_W  = 9.2
USBC_H  = 3.4

body = (
    cq.Workplane("XY")
    .box(POD_L, POD_W, POD_H)
    .edges("|Z").fillet(CORNER)
    .edges(">Z or <Z").fillet(2.0)
    .faces(">Z").shell(-WALL)
)

# Handlebar cradle on the bottom: cylinder subtracted, sized for BAR_MAX so
# silicone strap takes up the tolerance on smaller bars.
cradle_center = (0, 0, -POD_H / 2 + BAR_MAX / 2)
cradle = (
    cq.Workplane("XY")
    .workplane(offset=cradle_center[2])
    .moveTo(0, 0).circle(BAR_MAX / 2).extrude(POD_L, combine="s")
    .rotate((0, 0, 0), (0, 1, 0), 90)
    .translate((0, 0, 0))
)
# Strap channels running around the cradle.
strap_cuts = (
    cq.Workplane("XZ")
    .pushPoints([(+POD_L / 2 - 8, cradle_center[2]), (-POD_L / 2 + 8, cradle_center[2])])
    .rect(STRAP_W, BAR_MAX + 2 * WALL + 6).extrude(POD_W, combine="s")
)

# USB-C on the rear face behind a recessed flap (0.4mm pocket).
usbc = (
    cq.Workplane("XZ")
    .workplane(offset=POD_W / 2 + 0.01, centerOption="CenterOfBoundBox")
    .rect(USBC_W + 3.0, USBC_H + 2.0).extrude(-0.4, combine="s")
)
usbc_hole = (
    cq.Workplane("XZ")
    .workplane(offset=POD_W / 2 + 0.01, centerOption="CenterOfBoundBox")
    .rect(USBC_W, USBC_H).extrude(-WALL - 0.5, combine="s")
)

result = body.cut(cradle).cut(strap_cuts).cut(usbc).cut(usbc_hole)
```
<!-- /cadquery-base -->
