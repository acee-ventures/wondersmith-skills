---
name: led-wall-display-200usd
description: Production recipe for wall-mountable LED matrix displays (pixel art, word clocks, status boards) targeting ~$140-280 retail. 16×16 to 32×32 WS2812B matrix, ESP32 driver, USB-C 5V/3A input, diffused acrylic front, wood or acrylic frame. Use when the prompt describes a wall-hanging grid of addressable LEDs showing text, pixel art, weather, or notifications, retail $100-320.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: led-display
  budgetBand:
    bomMin: 45
    bomMax: 85
    retailTarget: 200
  manufacturingDays: 7
  componentCount: 12
  coreComponents:
    - "ESP32-S3 (for FastLED or WLED firmware + Wi-Fi)"
    - "WS2812B matrix 16×16 or 32×32 (individually addressable RGB)"
    - "5V 10A external DC brick (USB-C PD can't deliver 50W reliably for 32×32)"
    - "Level shifter (SN74HCT245) between ESP32 3.3V and LED 5V data"
    - "6000µF decoupling cap across 5V rail (inrush current)"
    - "3mm acrylic diffuser (milky opal, NOT frosted) — better pixel separation"
    - "MDF or oak frame, 12mm thick, keyhole mount hardware"
  safetyClass: class-III-lv-display
  complianceMust:
    - "FCC Part 15 class B (WS2812 data lines at 800kHz do radiate)"
    - "Use IEC 61558-certified external PSU; don't source grey-market bricks"
    - "UL 62368-1 for the display+PSU combo if sold in US retail"
    - "Wall mount must hold 3× unit weight (safety factor)"
  sourceRun: null
---

# LED Wall Display $200 Recipe

## Power budget sanity

Each WS2812 at full white ≈ 60mA. A 32×32 matrix = 1024 LEDs × 60mA = **61A** theoretical max. Real: software brightness limit to 30% typical + 50% peak = ~9A @ 5V = **45W**. That's why USB-C PD (max 100W at 20V/5A but only ~3A at 5V = 15W) isn't enough for anything over 16×16. Spec a 5V/10A external DC brick for 32×32.

## Diffuser thickness

Too thin (<2mm opal) and you see individual LED dots. Too thick (>5mm) and letters bleed. Sweet spot is 3mm milky opal cast acrylic (NOT frosted PMMA — frosted is matte finish only on one side, which pixelates differently).

## Cost sketch (20 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| ESP32-S3 board | 20 | $5.20 | $104 |
| WS2812B 16×16 panel | 20 | $14 | $280 |
| Level shifter + passives | 20 | $1.40 | $28 |
| Decoupling caps + power rail | 20 | $2.20 | $44 |
| 5V/10A external brick (UL cert) | 20 | $9.80 | $196 |
| 3mm cast acrylic diffuser | 20 | $6.40 | $128 |
| Wood frame + mount hardware | 20 | $12 | $240 |
| Assembly + QA | 20 | $3.80 | $76 |
| **Total** | | | **~$1096** |
| Per-unit landed | | | **~$55** |

<!-- cadquery-base -->
```python
import cadquery as cq

# ── LED matrix display frame ────────────────────────────────────────────────
# Outer wood/acrylic frame + inner rebate for the 3 mm diffuser + rear pocket
# for the WS2812 panel + ESP32 board. Keyhole mounts on top edge.
PANEL_L      = 180.0    # visible matrix width (16×16 @ 10mm pitch ≈ 160; margin added)
PANEL_W      = 180.0
FRAME_THK    = 14.0     # frame thickness (depth from wall)
FRAME_MARGIN = 16.0     # outer frame around visible panel
DIFF_DEPTH   = 3.5      # diffuser sits flush with front face
REAR_DEPTH   = 8.0      # pocket for matrix + ESP32
MOUNT_HOLE   = 5.0      # keyhole Ø
MOUNT_OFFSET = 22.0     # from top edge

OUTER_L = PANEL_L + 2 * FRAME_MARGIN
OUTER_W = PANEL_W + 2 * FRAME_MARGIN

frame = (
    cq.Workplane("XY")
    .box(OUTER_L, OUTER_W, FRAME_THK)
    .edges("|Z").fillet(2.0)
)

# Diffuser rebate (shallow pocket on the front).
diff_rebate = (
    cq.Workplane("XY")
    .workplane(offset=FRAME_THK / 2 - DIFF_DEPTH + 0.1)
    .rect(PANEL_L + 2.0, PANEL_W + 2.0).extrude(DIFF_DEPTH, combine="s")
)

# Rear pocket for the matrix PCB + ESP32.
rear_pocket = (
    cq.Workplane("XY")
    .workplane(offset=-FRAME_THK / 2)
    .rect(PANEL_L + 6.0, PANEL_W + 6.0).extrude(REAR_DEPTH, combine="s")
)

# Keyhole mounts: a circle + slot (slot under the circle) on top of the frame.
def keyhole(cx):
    circle = (
        cq.Workplane("XY")
        .workplane(offset=FRAME_THK / 2 - 1.0)
        .moveTo(cx, OUTER_W / 2 - MOUNT_OFFSET)
        .circle(MOUNT_HOLE / 2).extrude(-FRAME_THK + 2.0, combine="s")
    )
    slot = (
        cq.Workplane("XY")
        .workplane(offset=FRAME_THK / 2 - 1.0)
        .moveTo(cx, OUTER_W / 2 - MOUNT_OFFSET - 6)
        .rect(MOUNT_HOLE * 0.4, 8).extrude(-FRAME_THK + 2.0, combine="s")
    )
    return circle, slot

kh_l_c, kh_l_s = keyhole(-OUTER_L / 3)
kh_r_c, kh_r_s = keyhole(+OUTER_L / 3)

result = (
    frame
    .cut(diff_rebate).cut(rear_pocket)
    .cut(kh_l_c).cut(kh_l_s).cut(kh_r_c).cut(kh_r_s)
)
```
<!-- /cadquery-base -->
