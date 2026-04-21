---
name: wondersmith-battery-tool
description: Use the `wondersmith` CLI to design a cordless battery-powered hand tool (mini circular saws, sanders, screwdrivers, rotary tools, heat guns) with 18650/21700 pack, BLDC or brush motor, trigger + lock switch, overcurrent protection, glass-filled nylon body, targeting $70-250 retail. Trigger when the user asks for a DIY or hobbyist hand-held power tool. Do NOT trigger for professional contractor-grade tools, industrial drills, or anything requiring UL Listed status beyond the standard 62841.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: power-tool
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 38
    bomMax: 80
    retailTarget: 150
  homepage: https://wondersmith.app
---

# wondersmith — battery hand tool

## When to trigger this skill

The user's prompt should combine most of:

- **Tool class**: cordless, handheld, motor-driven
- **Use case**: DIY, hobby, light-duty, weekend maker, crafts
- **Power source**: 18650 or 21700 Li-ion pack (typically 2S or 3S)
- **Retail target**: $70-$250

Example prompts that match:

- "a mini circular saw for foamcore and thin plywood"
- "a palm sander for model-making"
- "a rotary tool for jewelry finishing"
- "a cordless screwdriver with a torque clutch"
- "a hobbyist heat gun for shrink wrap"

## Do NOT trigger when

- Pro-grade tool (sub-$500 for cordless drill class) → regulatory cost is too high for this pipeline
- Corded tool → different cert + thermal path
- Pneumatic tool → compressed-air systems out of scope
- Industrial / continuous-duty tool → duty cycle > 30% needs forced-air cooling, different archetype
- Weapons-adjacent (nail gun, grinder in specific configs) → red-team review needed; out of scope

## How to invoke wondersmith

### Quick preview (standard, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 150
```

### Full design with CAD + UL 62841 plan (deliberate, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 150 \
  --detach
wondersmith runs watch <runId>
```

Always deliberate for this archetype. Hand tools carry non-trivial injury risk; the critic checks for overcurrent protection, thermal cutoff, and double-insulation.

## What wondersmith produces for this archetype

- BOM: ~18 components, raw $38-$80, quote-ready at 1.5× ($57-$120)
- Artifacts (deliberate/deep):
  - BOM.csv with motor MPN, ESC / MOSFET spec, cell chemistry
  - `concept.step`, `concept.stl`, `concept.glb` — body + grip overmold + trigger
  - `concept.cadquery.py` — parametric body + battery compartment
  - `concept.kicad_sch` — motor control + BMS integration
  - `ORDER_INFO.json` — LeadArt plan, ~10 business days (longer assembly for motors)
- Suggested depth for this archetype: **deliberate** (safety mandatory)

## Prompt-writing tips for this archetype

Pin down with the user:

- **RPM and torque targets** ("20k RPM no-load", "5 Ncm stall") — sizes motor
- **Run time target** ("45 min continuous", "15 min bursts with cool-down")
- **Chuck / collet size** (1/4" hex driver, 3 mm rotary tool collet, nothing changeable, etc.)
- **User hand size** (ergonomics differ child-safe vs. adult-grip-strength)

Default to BLDC motor if the duty cycle is >10%. Brush motor only for very short-burst tools (screwdriver).

## Reading the output

- **BOM.csv**: verify BMS includes **cell balancing** (not just overcurrent). 18650 packs without balance ICs drift and fail early.
- **3D viewer**: check for overmolded TPE grip (required for hand comfort + vibration). Flag bare nylon grips.
- **Final-report thermal section**: motor casing PTC thermistor at 90°C cutoff is mandatory for this archetype. Flag absence.
- **Trigger switch**: should be Hall-effect sensor, NOT rheostat (rheostats burn out under PWM).

## Compliance heads-up

- **UL 62841** (US) or **EN 62841** (EU) — primary handheld power tool safety cert. This is the big one. ~$3-5k per configuration at cert lab.
- **Double insulation** marking OR Class I with proper ground path. Most battery tools are Class III (low-voltage), simpler.
- **Overcurrent protection** — must trip within 100 ms at 2× rated current. Critic checks this.
- **FCC Part 15 Class B** — brushed motors radiate. Need ferrite suppression on power leads.
- **UN 38.3** — Li-ion pack shipping. Non-negotiable for international retail.
- **IPX5 on vents** — for sanders especially, conductive dust exclusion is required.
- **REACH SVHC + RoHS 3** — EU.

Tell the user upfront that certification is the dominant cost above raw BOM — plan $5-8k cert budget for a UL 62841 handheld. This is not optional for US retail.
