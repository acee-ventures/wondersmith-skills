---
name: plant-care-device-110usd
description: Production recipe for houseplant care devices targeting ~$60-160 retail — soil moisture + light meters, grow-light arrays, self-watering valves, BLE plant monitors. Multi-sensor array (soil moisture capacitive probe, light, ambient temp/humidity), single AA/LiPo, indoor-only IPX2. Use when the prompt describes a device that helps keep plants alive in a home or office, retail $40-180.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: plant-care
  budgetBand:
    bomMin: 16
    bomMax: 38
    retailTarget: 110
  manufacturingDays: 7
  componentCount: 14
  coreComponents:
    - "ESP32-C3 (tiny, BLE + Wi-Fi for sync-to-app)"
    - "Capacitive soil moisture probe (NOT resistive — resistive corrodes in days)"
    - "BH1750 ambient light sensor (plant-grade, works through green filter)"
    - "HDC2080 or SHT40 temp/humidity"
    - "Optional: water level via float switch + solenoid valve (12V latch) for self-watering models"
    - "LiPo 500mAh with TP4056 + solar supplement pad (2V/40mA polycrystalline)"
    - "IP67 potted PCB at probe tip (potting epoxy, NOT silicone — soil chemistry attacks silicone)"
    - "ASA plastic probe stem (UV-resistant; PLA cracks in 3 months under grow lights)"
  safetyClass: indoor-electronics-near-soil
  complianceMust:
    - "FCC Part 15 class B for BLE/Wi-Fi"
    - "Potting compound must be UL94 V-0 (flame-resistant) if near grow-light heat"
    - "No lead in solder on probe tip (plant contact, even if indirect via soil)"
    - "EN 60335-1 general safety for low-voltage household appliances"
  sourceRun: null
---

# Plant Care Device $110 Recipe

## Sensor placement math

- Soil probe needs ≥60mm in-soil to read root-zone moisture (topsoil dries first; roots aren't there).
- Light sensor needs green-filter correction — plants don't use green; a raw lux reading over-counts.
- Ambient T/RH should be in the *air*, not in the soil envelope. Two sensors on opposite ends.

## Power profile

Deep-sleep ESP32-C3 between reads: 8µA. Wake every 15 min, sample + BLE adv 30s, sleep. Average ~25µA. With 500mAh LiPo + solar supplement pad on a windowsill, runs forever. No solar pad = ~9-12 months per charge.

## Don't claim "grow light"

Unless the product has ≥15W of red+blue 5000K LED, it's an indicator, not a grow-light. Customers sue over this.

## Cost sketch (50 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| ESP32-C3 module | 50 | $2.40 | $120 |
| Capacitive soil probe | 50 | $1.80 | $90 |
| BH1750 + HDC2080 | 50 | $1.60 | $80 |
| LiPo + charger + (optional) solar | 50 | $3.40 | $170 |
| Potting compound (service fee) | 50 | $1.20 | $60 |
| ASA probe stem + body | 50 | $3.80 | $190 |
| Assembly + QA | 50 | $2.20 | $110 |
| **Total** | | | **~$820** |
| Per-unit landed | | | **~$16** |

<!-- cadquery-base -->
```python
import cadquery as cq

# ── Soil-probe plant monitor ────────────────────────────────────────────────
# Long ASA probe stem (root-zone reach) + UV-resistant head with BH1750
# window + capacitive touch button + optional solar pad pocket.
PROBE_L     = 85.0
PROBE_W     = 12.0     # probe stem is rectangular (structural against soil drag)
PROBE_T     = 6.0
HEAD_L      = 50.0
HEAD_W      = 38.0
HEAD_H      = 18.0
CORNER_R    = 3.0
WALL        = 1.8
LIGHT_D     = 6.0      # BH1750 window
BTN_D       = 7.0
SOLAR_W     = 28.0
SOLAR_H     = 16.0

# Head: rounded rectangular body, shelled from the bottom for internals.
head = (
    cq.Workplane("XY")
    .box(HEAD_L, HEAD_W, HEAD_H)
    .edges("|Z").fillet(CORNER_R)
    .edges(">Z or <Z").fillet(1.5)
    .faces("<Z").shell(-WALL)
)

# Light window on +Z face (top).
light = (
    cq.Workplane("XY")
    .workplane(offset=HEAD_H / 2 - WALL - 0.1)
    .moveTo(HEAD_L / 2 - 10, 0).circle(LIGHT_D / 2).extrude(WALL + 0.5, combine="s")
)

# Capacitive touch on +Z face.
btn = (
    cq.Workplane("XY")
    .workplane(offset=HEAD_H / 2 - WALL - 0.1)
    .moveTo(-HEAD_L / 2 + 12, 0).circle(BTN_D / 2).extrude(WALL + 0.5, combine="s")
)

# Solar pad recess on +Z (leaves 0.4mm skin so we keep IP rating).
solar = (
    cq.Workplane("XY")
    .workplane(offset=HEAD_H / 2 - 0.4)
    .rect(SOLAR_W, SOLAR_H).extrude(0.4 - 0.01, combine="s")
)

# Probe stem coming out of the bottom.
probe = (
    cq.Workplane("XY")
    .workplane(offset=-HEAD_H / 2)
    .rect(PROBE_W, PROBE_T).extrude(-PROBE_L)
    .edges("|Z").fillet(1.0)
)
# Chamfer the probe tip for soil entry.
probe = probe.faces("<Z").chamfer(0.8)

result = head.cut(light).cut(btn).cut(solar).union(probe)
```
<!-- /cadquery-base -->
