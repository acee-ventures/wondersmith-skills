---
name: wondersmith-desk-lamp
description: Use the `wondersmith` CLI to design a desk / bedside / shelf lamp with dimming or color-temp control (high-CRI COB LED, USB-C wall-wart, capacitive or rotary dim, anodized aluminum + weighted base) targeting $60-200 retail. Trigger when the user asks for a task light, accent light, reading lamp, or ambient puck. Do NOT trigger for smart-home bulbs, mains-voltage ceiling fixtures, or grow lights.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: lighting
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 22
    bomMax: 48
    retailTarget: 120
  homepage: https://wondersmith.app
---

# wondersmith — desk lamp

## When to trigger this skill

The user's prompt should combine most of:

- **Primary purpose**: illumination (task / accent / reading / ambient)
- **Form factor**: desk / bedside / shelf / wall-mounted puck
- **Power**: USB-C wall-wart (not mains-voltage; not battery-primary)
- **Control**: dimming and/or color-temp tuning (warm-cool) via capacitive touch or rotary encoder
- **Retail target**: $60-$200 range

Example prompts that match:

- "a minimalist desk lamp with a wood base and warm-to-cool tunable light"
- "a clip-on reading light that can be dimmed by tapping"
- "an accent puck that sits on a shelf and glows amber at night"
- "an architect-style task lamp with a CRI-90 bar"

## Do NOT trigger when

- Smart-home bulb (Wi-Fi Matter / Thread) → out of scope for this archetype; the user likely wants a commercial Hue-class product, not hand-assembled
- Ceiling / wall fixture wired to mains → out of scope (needs UL 1598, different cert path)
- Grow light → use `wondersmith-plant-care` (which includes a grow-array option)
- Wall-mounted pixel display → use `wondersmith-led-wall-display`
- Portable / battery-powered night light → consider `wondersmith-desk-electronics` if BLE is wanted

## How to invoke wondersmith

### Quick preview (standard depth, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 120
```

Good for BOM gut-check and a concept render.

### Full design with CAD + driver (deliberate depth, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 120 \
  --detach
wondersmith runs watch <runId>
```

Lamps benefit from deliberate because heat management is load-bearing — the critic checks thermal spread math.

## What wondersmith produces for this archetype

- BOM: ~13 components, raw $22-$48, quote-ready at 1.5× ($33-$72)
- Artifacts (deliberate/deep):
  - BOM.csv with driver IC + LED package choices
  - `concept.step`, `concept.stl`, `concept.glb` — lamp body + shade
  - `concept.cadquery.py` — parametric heat-spreader + base-weight cavity
  - `concept.kicad_sch` — driver + MCU schematic
  - `ORDER_INFO.json` — LeadArt plan, ~7 business days
- Suggested depth for this archetype: **deliberate** (heat + CRI validation matters)

## Prompt-writing tips for this archetype

Push the user to specify:

- **Lumen target** ("~400 lumens like a good desk lamp")
- **Color temperature** (fixed 3000K / 2700K+6500K tunable / full RGB)
- **Footprint** ("base fits on a 10cm square", "pocket-sized")
- **Visual style** ("Dieter Rams minimalist", "walnut + brass", "industrial concrete")

If they don't mention CRI, default-assume CRI≥90 (anyone asking for a design-quality lamp cares about color rendering even if they don't know the term).

## Reading the output

- **BOM.csv**: `LED package` row — check MPN actually supports the claimed CRI. Cheap COBs mark "CRI 85" but measure 80.
- **Thermal section** in the final-report artifact: aluminum heat-spreader area should be ≥8 cm²/W of LED dissipation. Flag to user if the design skimps.
- **3D viewer**: verify the weighted base cavity is vented (not sealed — thermal buildup) and that the USB-C entry point has strain relief.

## Compliance heads-up

- **UL 153** (US) or **EN 60598-2-4** (EU) — portable lamp safety. Cert path depends on primary market.
- **IEC 62471 photobiological safety** — lamp is usually exempt group at <400 lumens at 20cm viewing distance. Above that, specify the blue-light limit.
- **FCC Part 15 Class B** — PWM dimming radiates EMI at 30-300 kHz. Need an LC filter at the driver output; the deliberate run surfaces this.
- **Energy Star 2.0** — only if the user wants to claim efficiency. Adds test cost; don't opt in unless they ask.
- **Mains isolation** — if user insists on a wall-plug adapter rather than USB-C wall-wart, push back: mains isolation adds $15-20 BOM and a double-insulation enclosure requirement. USB-C PD at 5V/3A covers most desk lamps.
