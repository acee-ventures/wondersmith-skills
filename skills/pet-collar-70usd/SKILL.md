---
name: pet-collar-70usd
description: Production recipe for dog and cat collar-mounted trackers and IDs targeting ~$40-100 retail — BLE presence beacons, activity monitors, light-up safety tags. Small, light, chew-resistant, recharging via magnetic contacts. Use when the prompt describes a clip-on or collar-threaded wearable for dogs or cats, retail $25-$110.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: pet-wearable
  budgetBand:
    bomMin: 10
    bomMax: 22
    retailTarget: 70
  manufacturingDays: 7
  componentCount: 11
  coreComponents:
    - "nRF52805 (smallest nRF52, 1.6mm pitch QFN) — BLE only, no GPS at this tier"
    - "Coin LiPo 80-150mAh (NOT CR2032 — chewable, swallowable)"
    - "Magnetic pogo charging contacts (2-pin) — no USB port to chew"
    - "LIS2DW12 ultra-low-power accelerometer"
    - "RGB LED on underside (bark of a tree shape, covered by collar loop)"
    - "Injection-molded polycarbonate shell, snap-fit + ultrasonically welded"
    - "Stainless 304 D-ring clip (non-nickel — some dogs allergic)"
  safetyClass: pet-wearable-chew-resistant
  complianceMust:
    - "Chew test: 50N bite force for 30s, no enclosure crack (pet industry norm)"
    - "Swallow safety: no parts <15mm on any axis (choking hazard for medium+ breeds)"
    - "IPX6 minimum (dogs swim and shake)"
    - "Battery enclosure must survive 3m drop on concrete"
    - "FCC Part 15 for BLE (cert via pre-cert nRF module if possible)"
  sourceRun: null
---

# Pet Collar $70 Recipe

## Hard constraints

- **No exposed batteries**. Ever. Vet bills from swallowed CR2032s are a meme in pet-product subreddits for a reason.
- **No dangling lanyards or ribbons** — strangulation risk if collar catches on fence.
- **Color**: black, navy, forest green absorb LED light. High-vis orange or yellow shells hurt LED visibility. Think through the tradeoff.

## BLE + range realism

nRF52805 at +4dBm with PCB antenna in a plastic shell ≈ 40-80m line-of-sight outdoors. DO NOT claim "track your dog anywhere" — that requires GPS + LTE-M, which blows the $70 budget.

## Cost sketch (100 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| nRF52805 module | 100 | $2.40 | $240 |
| Coin LiPo 100mAh | 100 | $1.80 | $180 |
| Magnetic charging contacts | 100 | $0.90 | $90 |
| Accel + LED | 100 | $1.30 | $130 |
| PC shell + weld | 100 | $1.60 | $160 |
| D-ring + hardware | 100 | $0.80 | $80 |
| QA + chew test (amortized) | 1 | $150 | $150 |
| **Total** | | | **~$1030** |
| Per-unit landed | | | **~$10.30** |

<!-- cadquery-base -->
```python
import cadquery as cq

# ── Pet collar pod (chew-resistant, ultrasonically welded) ──────────────────
# Teardrop pod threaded through the collar via two slots on the back.
# Magnetic pogo contacts on the underside; LED on the tail; no user-openable
# battery door. Ultrasonic weld seam at the equator.
POD_L       = 36.0
POD_W       = 22.0
POD_H       = 11.0
CORNER_R    = 4.0
WALL        = 1.8
COLLAR_W    = 18.0   # slot width (fits up to 18mm webbing)
COLLAR_GAP  = 4.0    # slot height
POGO_D      = 2.4
LED_D       = 3.0

pod = (
    cq.Workplane("XY")
    .box(POD_L, POD_W, POD_H)
    .edges("|Z").fillet(CORNER_R)
    .edges(">Z or <Z").fillet(2.5)
)
shell = pod.faces(">Z").shell(-WALL)

# Collar slots: two vertical slits spaced so webbing threads flat.
slots = (
    cq.Workplane("XY")
    .pushPoints([(+POD_L / 2 - 4, 0), (-POD_L / 2 + 4, 0)])
    .rect(COLLAR_GAP, COLLAR_W).extrude(POD_H + 1, both=True, combine="s")
)

# Pogo contacts on the bottom.
pogos = (
    cq.Workplane("XY")
    .workplane(offset=-POD_H / 2)
    .pushPoints([(+5.0, 0), (-5.0, 0)])
    .circle(POGO_D / 2).extrude(-WALL - 0.5, combine="s")
)

# LED on the tail (+X face).
led = (
    cq.Workplane("YZ")
    .workplane(offset=POD_L / 2 + 0.1, centerOption="CenterOfBoundBox")
    .circle(LED_D / 2).extrude(-WALL - 0.5, combine="s")
)

result = shell.cut(slots).cut(pogos).cut(led)
```
<!-- /cadquery-base -->
