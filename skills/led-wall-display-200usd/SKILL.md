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
