---
name: wearable-fitness-150usd
description: Production recipe for minimalist wearable fitness/health bands targeting ~$100-180 retail. nRF52-class BLE SoC, single accelerometer + optional PPG, haptic coin motor, curved OLED or LED bar indicator, LiPo pouch cell, silicone + injection-molded polycarbonate enclosure, IPX7 splash-resistant. Use when the prompt describes a wristband/bracelet/clip that tracks movement, posture, sleep, or heart-rate and pairs to a phone, retail $80-$200.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: wearable
  budgetBand:
    bomMin: 32
    bomMax: 65
    retailTarget: 150
  manufacturingDays: 9
  componentCount: 18
  coreComponents:
    - "nRF52832 / nRF52840 module (Raytac MDBT42Q preferred for FCC-modular)"
    - "LSM6DSO 6-axis IMU (accelerometer + gyro) via I2C"
    - "Optional MAX30102 PPG for heart-rate (increases skin-contact surface spec)"
    - "1.1\" curved OLED (SSD1315) OR 5×7 LED bar matrix (much cheaper)"
    - "LRA haptic coin motor 10×2.7mm (not ERM — battery life)"
    - "LiPo pouch 80-150mAh in welded Kapton shield"
    - "TP4054 + DW01 protection; USB-C pogo or magnetic charging"
    - "Injection-molded PC body + TPU/silicone strap (overmolded snap)"
  safetyClass: wearable-skin-contact
  complianceMust:
    - "FCC Part 15 modular BLE grant (use pre-certified module)"
    - "Skin-contact biocompatibility: ISO 10993-5 + 10993-10 if PPG touches skin"
    - "IPX7 ingress rating (30min at 1m, fully submerged)"
    - "EN 62133 battery cell safety"
  sourceRun: null
---

# Wearable Fitness $150 Recipe

## When to use this skill

Combine any of: wristband / bracelet / posture / fitness / sleep / activity / vibration reminder / clip-on tracker → $80-200.

## Design anchors

- **Always BLE, never Wi-Fi** at this price — Wi-Fi burns 5-10× battery and needs antenna real estate.
- **Haptic > screen** for a "nudge" product. LED bar indicator beats curved OLED unless the prompt explicitly needs text.
- **Battery life target**: ≥7 days typical use at 1 min/day of display + 5 haptic events/day. IMU sampling at 25Hz → ~0.2mA average.
- **Charging contact** should be pogo or magnetic — exposed USB-C kills IPX7 claim.

## Firmware notes for Step 3 (structural-engineering)

Reference nRF Connect SDK (NCS) with Zephyr. Key power profile:
- System OFF sleep between events: 0.3µA
- BLE advertising 1Hz: ~8µA
- Active IMU sample + step-count: ~1.2mA for ~1ms every 40ms → ~30µA average

Don't spec Nordic Thingy; that's dev hardware, not production.

## Strap compliance

If strap is TPU on skin for >4h/day: nickel release test (EN 1811) if any metal in snap.

## Cost sketch (50 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| MDBT42Q module (FCC-cert BLE) | 50 | $5.20 | $260 |
| LSM6DSO IMU | 50 | $2.40 | $120 |
| LED bar driver + LEDs | 50 | $1.80 | $90 |
| LRA haptic motor | 50 | $1.10 | $55 |
| LiPo 100mAh pouch | 50 | $2.20 | $110 |
| Charger IC + protection | 50 | $0.95 | $47.50 |
| Injection-molded PC body (low-vol mold amortized) | 50 | $4.80 | $240 |
| TPU strap overmolded | 50 | $2.60 | $130 |
| Assembly + QA | 50 | $3.40 | $170 |
| **Total** | | | **~$1222** |
| Per-unit landed | | | **~$24** |

<!-- cadquery-base -->
```python
import cadquery as cq

# ── Parametric wearable body (wrist strap pod) ──────────────────────────────
# Rounded rectangular shell with strap slots, charging pogo pads on the
# back, display window on the top. PCB + LiPo + LRA stack from bottom→top.
POD_L     = 34.0   # along strap axis
POD_W     = 22.0
POD_H     = 9.8
CORNER_R  = 4.0
WALL      = 1.6
DISP_W    = 14.0   # display window
DISP_H    = 8.0
STRAP_W   = 18.0   # strap slot width
STRAP_H   = 2.6    # strap slot slit height
POGO_D    = 2.2    # charging pogo pad through-hole × 2

shell = (
    cq.Workplane("XY")
    .box(POD_L, POD_W, POD_H)
    .edges("|Z").fillet(CORNER_R)
    .edges(">Z or <Z").fillet(1.5)
    .faces(">Z").shell(-WALL)
)

disp = (
    cq.Workplane("XY")
    .workplane(offset=POD_H / 2 - WALL)
    .rect(DISP_W, DISP_H).extrude(WALL + 0.5, combine="s")
)

strap_slots = (
    cq.Workplane("YZ")
    .workplane(offset=POD_L / 2 - 2.0, centerOption="CenterOfBoundBox")
    .rect(STRAP_W, STRAP_H).extrude(-3.0, combine="s")
)
strap_slots_back = (
    cq.Workplane("YZ")
    .workplane(offset=-POD_L / 2 + 2.0, centerOption="CenterOfBoundBox")
    .rect(STRAP_W, STRAP_H).extrude(3.0, combine="s")
)

pogos = (
    cq.Workplane("XY")
    .workplane(offset=-POD_H / 2)
    .pushPoints([(+5.0, 0), (-5.0, 0)])
    .circle(POGO_D / 2).extrude(-WALL - 0.5, combine="s")
)

result = shell.cut(disp).cut(strap_slots).cut(strap_slots_back).cut(pogos)
```
<!-- /cadquery-base -->
