---
name: wondersmith-led-wall-display
description: Use the `wondersmith` CLI to design a wall-mountable LED matrix display (16×16 to 32×32 WS2812B + ESP32, external 5V DC brick, diffused acrylic front, wood or acrylic frame) for pixel art, word clocks, status boards, or notification panels, targeting $100-320 retail. Trigger when the user wants a wall-hanging grid of addressable LEDs. Do NOT trigger for commercial digital signage, indoor TV-replacement displays, or single-strip ambient lighting.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: led-display
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 45
    bomMax: 85
    retailTarget: 200
  homepage: https://wondersmith.app
---

# wondersmith — LED wall display

## When to trigger this skill

The user's prompt should combine most of:

- **Form factor**: wall-mounted frame, square or rectangular grid
- **Output**: individually addressable RGB pixels (WS2812B / SK6812 / APA102)
- **Content**: pixel art, word clock, weather, calendar, Slack/Discord status, notifications
- **Control**: Wi-Fi via ESP32, usually WLED or FastLED firmware
- **Retail target**: $100-$320

Example prompts that match:

- "a word clock that shows the time in English words"
- "a 32×32 pixel art frame that syncs to my Home Assistant"
- "a notification panel for my Discord server status"
- "a weather mood lamp that shows rain as falling pixels"

## Do NOT trigger when

- Commercial digital signage / 4K display → out of scope (panel drive complexity)
- TV-replacement wall display → needs HDMI + video decode, different stack
- Single-strip backlight under a desk → use `wondersmith-desk-lamp` + request RGB
- Large-format stadium / arena display → safety cert + structural engineering out of scope
- Grow-light panel → use `wondersmith-plant-care` (grow-array option)

## How to invoke wondersmith

### Quick preview (standard, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 200
```

### Full design with CAD + power budget (deliberate, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 200 \
  --detach
wondersmith runs watch <runId>
```

Deliberate is strongly recommended — the power budget math (matrix size × current × diffuser depth) is the single most common failure in DIY versions of this archetype; the critic catches it.

## What wondersmith produces for this archetype

- BOM: ~12 components, raw $45-$85, quote-ready at 1.5× ($68-$128)
- Artifacts (deliberate/deep):
  - BOM.csv with matrix size, PSU spec, frame material
  - `concept.step`, `concept.stl`, `concept.glb` — frame + diffuser + mount
  - `concept.cadquery.py` — parametric frame + keyhole mount
  - `concept.kicad_sch` — ESP32 + level-shifter + PSU input
  - `ORDER_INFO.json` — LeadArt plan, ~7 business days
- Suggested depth for this archetype: **deliberate**

## Prompt-writing tips for this archetype

Ask the user:

- **Matrix resolution** (16×16 fits text tightly; 32×32 comfortable; 64×64 is a bigger cost + power step)
- **Frame material** (MDF cheap, oak premium, acrylic minimalist)
- **Content source** (local pixel art, Home Assistant, custom web app, static ROM)
- **Wall-size footprint** ("fits above my desk", "small square in the kitchen")

Default to 16×16 at this price point. Only go 32×32 if the user explicitly wants more detail; 64×64 exceeds this archetype's budget.

## Reading the output

- **BOM.csv**: verify PSU is **external DC brick**, IEC 61558-certified (UL recognized). USB-C PD cannot deliver the 45-60 W a 32×32 matrix needs at even 30% brightness. Flag if USB-C appears as primary power.
- **3D viewer**: diffuser thickness should be 3 mm milky opal cast acrylic (NOT frosted PMMA — frosted pixelates unevenly). Check for ventilation gaps between matrix and diffuser (1-3 mm air gap).
- **Final-report**: power-budget section should show watts at 30% global brightness × 50% peak-pixel brightness. If watts > PSU rating × 0.8, the design will brown out on full-white.

## Compliance heads-up

- **FCC Part 15 Class B** — WS2812 data lines at 800 kHz radiate. Ferrite bead on the data line + shielded cable recommended. ESP32 Wi-Fi pushes this.
- **UL 62368-1** — audio / video / IT equipment cert if sold in US retail. Covers display + external PSU combo.
- **External PSU** — must be IEC 61558 listed. Don't source grey-market bricks. Tell wondersmith to spec Meanwell GS or equivalent.
- **Wall mount** — must hold 3× unit weight (safety factor). The CAD should include a keyhole that fits a #8 drywall anchor minimum.
- **Energy Star** — optional; not required.
- **RoHS 3** — EU.

If the user wants network sync / notification APIs, steer them toward WLED firmware — it's open-source, actively maintained, and has MQTT + HA integration out of the box. Don't invent a custom cloud.
