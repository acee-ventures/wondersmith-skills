---
name: kitchen-sensor-80usd
description: Production recipe for countertop kitchen measurement devices targeting ~$50-90 retail — temperature probes, scales, pH pens, water hardness meters, fridge/freezer watchers. Single-purpose sensor, 1.3" OLED or 7-seg readout, single coin cell or AAA, food-contact NSF material where applicable. Use when the prompt describes a small handheld or countertop sensor used near food or drink, retail $40-$100.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: kitchen-sensor
  budgetBand:
    bomMin: 12
    bomMax: 28
    retailTarget: 80
  manufacturingDays: 6
  componentCount: 10
  coreComponents:
    - "STM32L0-class MCU or ATmega328 + RN4020 (if BLE) — NO Wi-Fi on coin-cell products"
    - "Sensor per use-case: MAX31855 K-type thermocouple (BBQ probe), HX711 + load cell (scale), pH glass probe + ADS1115 (pen), HDC1080 (ambient temp/humidity)"
    - "1.3\" SSD1306 OLED (I2C, 10mA) OR 3-digit 7-seg + CD4511 decoder"
    - "CR2032 coin cell (soldered-in or carrier — NEVER user-accessible on kids-reachable unit)"
    - "Food-contact stainless 304 probe (NSF-rated) if sensor touches food"
    - "Silicone overmolded buttons, no moving mechanical keys (liquid ingress)"
  safetyClass: food-contact-nsf
  complianceMust:
    - "NSF/ANSI 51 materials if sensor contacts food directly"
    - "FDA 21 CFR 177 for plastics contacting food >4s"
    - "IPX4 minimum for countertop items (IPX5 preferred for near-sink)"
  sourceRun: null
---

# Kitchen Sensor $80 Recipe

## When to use

Single-sensor measurement + readout, kitchen context, $40-100. Don't use this skill for smart plugs, Wi-Fi thermometers ≥$150 (those go to desk-electronics), or IoT bottle trackers.

## Design anchors

- **Single cell**, weeks of standby. Wi-Fi is banned unless the prompt explicitly demands remote logging.
- **OLED over 7-seg** only if the prompt mentions graphs or multi-line readouts. 7-seg is cheaper + more readable in kitchen lighting.
- **Probe detach** via 3.5mm TRS or 4-pin M8 connector if the sensor tip is exposed to heat >80°C.

## Cost sketch (30 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| MCU + passives | 30 | $1.20 | $36 |
| Sensor (thermocouple or HX711+cell) | 30 | $3.50 | $105 |
| OLED or 7-seg | 30 | $2.20 | $66 |
| CR2032 + clip | 30 | $0.40 | $12 |
| Enclosure (PC/ABS) | 30 | $2.80 | $84 |
| Probe + cable + connector | 30 | $4.10 | $123 |
| Assembly + cert paperwork | 30 | $2.60 | $78 |
| **Total** | | | **~$504** |
| Per-unit landed | | | **~$17** |

<!-- cadquery-base -->
```python
import cadquery as cq

# ── Handheld kitchen sensor: cylindrical handle + probe ferrule ─────────────
# Handle holds PCB + coin cell; a 3.5 mm TRS jack on the bottom accepts
# a detachable stainless probe. Display window on the top face.
HANDLE_D   = 24.0  # handle outer Ø
HANDLE_L   = 82.0
WALL       = 1.8
DISP_W     = 18.0  # display cutout (W along axis × H around axis)
DISP_H     = 10.0
BTN_D      = 4.0
TRS_D      = 6.2   # 3.5 mm TRS jack barrel Ø + tolerance

handle = (
    cq.Workplane("XY")
    .circle(HANDLE_D / 2).extrude(HANDLE_L)
    .faces(">Z").shell(-WALL)
)

# Display window on +Y side, positioned upper third of the handle.
disp_cut = (
    cq.Workplane("XZ")
    .workplane(offset=HANDLE_D / 2 + 0.1, centerOption="CenterOfBoundBox")
    .moveTo(0, HANDLE_L / 2 - 14)
    .rect(DISP_W, DISP_H).extrude(-WALL - 0.2, combine="s")
)

btn_cut = (
    cq.Workplane("XZ")
    .workplane(offset=HANDLE_D / 2 + 0.1, centerOption="CenterOfBoundBox")
    .moveTo(0, 4)
    .circle(BTN_D / 2).extrude(-WALL - 0.2, combine="s")
)

trs_hole = (
    cq.Workplane("XY")
    .workplane(offset=HANDLE_L / 2 - HANDLE_L / 2)
    .circle(TRS_D / 2).extrude(-WALL - 0.2, combine="s")
)

result = handle.cut(disp_cut).cut(btn_cut).cut(trs_hole)
```
<!-- /cadquery-base -->
