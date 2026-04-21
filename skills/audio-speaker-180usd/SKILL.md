---
name: audio-speaker-180usd
description: Production recipe for portable / bookshelf Bluetooth speakers targeting ~$120-240 retail. Single-driver or 2-way, Class-D amp, BLE 5.2 audio (LE Audio preferred), USB-C charging, injection-molded ABS or sand-cast aluminum grille. Use when the prompt describes a wireless speaker, portable boombox, or bookshelf audio unit, retail $80-260.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: audio
  budgetBand:
    bomMin: 38
    bomMax: 85
    retailTarget: 180
  manufacturingDays: 8
  componentCount: 16
  coreComponents:
    - "ESP32-S3 or Qualcomm QCC30xx for Bluetooth Audio (QCC if aptX HD needed)"
    - "TI TPA3116D2 Class-D amp (50W peak @24V; downspec to TPA3110 for 15W)"
    - "Dayton 2\" full-range driver (DC28F) OR single 3\" + passive radiator"
    - "LiFePO4 3.2V 2500mAh cell (better cycle life than LiPo; safer under 60°C)"
    - "BQ25895 USB-PD 15V charger IC for quick-charge"
    - "Injection-molded ABS body + perforated steel grille (powder coated)"
    - "Silicone rubber foot pads (vibration dampening to avoid rattle)"
  safetyClass: portable-audio-class-III
  complianceMust:
    - "FCC Part 15 class B + SDoC for BT-SIG qualification"
    - "BT-SIG Declaration ID (required for BLE branding)"
    - "EN 50332-1 max SPL for portable audio (EU)"
    - "UN 38.3 for shipping LiFePO4 cells"
  sourceRun: null
---

# Portable Speaker $180 Recipe

## The two biggest traps

1. **BT audio latency**. Standard A2DP ≈ 150-200ms. That's fine for music, bad for video. If the prompt says "TV soundbar", this skill is WRONG — go to larger category with LE Audio + LC3 or wired HDMI ARC.
2. **Driver/enclosure volume math**. 3" driver needs ~1-2L cabinet volume for decent low-end. Less = boxy, tinny. More = wasted. Compute the Thiele-Small parameters from the driver spec before locking the enclosure shape.

## Ferrite shielding

Class-D amp switching at 500kHz will couple into BT radio if unshielded. Keep the amp board ≥25mm from the antenna and add a ferrite bead on the speaker wires.

## Cost sketch (40 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| ESP32-S3 module + BT | 40 | $4.80 | $192 |
| TPA3116D2 Class-D amp | 40 | $3.20 | $128 |
| Dayton 3\" driver + passive | 40 | $8.60 | $344 |
| LiFePO4 2500mAh | 40 | $6.40 | $256 |
| Charger + passives | 40 | $2.80 | $112 |
| ABS body (low-vol tooling amortized) | 40 | $6.80 | $272 |
| Steel grille + powder coat | 40 | $3.40 | $136 |
| Assembly + QA + BT cert (amortized) | 40 | $4.20 | $168 |
| **Total** | | | **~$1608** |
| Per-unit landed | | | **~$40** |

<!-- cadquery-base -->
```python
import cadquery as cq

# ── Cylindrical portable BT speaker ─────────────────────────────────────────
# Grille + driver cutout on one face, passive radiator on the opposite face,
# USB-C + aux jack + button row along the side.
BODY_D      = 90.0
BODY_L      = 130.0
WALL        = 2.4
DRIVER_D    = 58.0   # main 2.5" full-range driver cutout
RADIATOR_D  = 60.0   # passive radiator cutout
USBC_W      = 9.2
USBC_H      = 3.4
AUX_D       = 6.2
BTN_D       = 6.0

body = (
    cq.Workplane("XY")
    .circle(BODY_D / 2).extrude(BODY_L)
    .faces(">Z").shell(-WALL)
)

# Driver on +Z face, radiator on -Z.
driver = (
    cq.Workplane("XY")
    .workplane(offset=BODY_L - WALL - 0.1)
    .circle(DRIVER_D / 2).extrude(WALL + 0.5, combine="s")
)
radiator = (
    cq.Workplane("XY")
    .workplane(offset=-0.1)
    .circle(RADIATOR_D / 2).extrude(-WALL - 0.5, combine="s")
)

# Port row on +Y side.
side_z = BODY_L / 2
usbc = (
    cq.Workplane("XZ")
    .workplane(offset=BODY_D / 2 - WALL - 0.1, centerOption="CenterOfBoundBox")
    .moveTo(0, side_z).rect(USBC_W, USBC_H).extrude(WALL + 2, combine="s")
)
aux = (
    cq.Workplane("XZ")
    .workplane(offset=BODY_D / 2 - WALL - 0.1, centerOption="CenterOfBoundBox")
    .moveTo(-14, side_z).circle(AUX_D / 2).extrude(WALL + 2, combine="s")
)
btns = (
    cq.Workplane("XZ")
    .workplane(offset=BODY_D / 2 - WALL - 0.1, centerOption="CenterOfBoundBox")
    .pushPoints([(+12, side_z + 12), (+12, side_z), (+12, side_z - 12)])
    .circle(BTN_D / 2).extrude(WALL + 2, combine="s")
)

result = body.cut(driver).cut(radiator).cut(usbc).cut(aux).cut(btns)
```
<!-- /cadquery-base -->
