---
name: wondersmith-wearable-fitness
description: Use the `wondersmith` CLI to design a minimalist wristband / bracelet / clip fitness or health tracker (nRF52 BLE SoC, IMU, optional PPG, curved OLED or LED bar, LiPo pouch, silicone + PC enclosure, IPX7) targeting $80-200 retail. Trigger when the user asks for a body-worn BLE tracker that pairs to a phone. Do NOT trigger for medical-grade devices, smartwatches with full touchscreens, or hearables.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: wearable
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 32
    bomMax: 65
    retailTarget: 150
  homepage: https://wondersmith.app
---

# wondersmith — wearable fitness band

## When to trigger this skill

The user's prompt should combine at least two of:

- **Form factor**: wristband / bracelet / clip-on / pendant / ankle-strap
- **Sensing**: movement, posture, sleep, heart-rate, step count, haptic reminder
- **Connectivity**: BLE pair to a phone app
- **Retail target**: $80-$200

Example prompts that match:

- "a posture-reminder bracelet that vibrates when I slouch"
- "a discreet clip-on sleep tracker for travel"
- "a minimalist running band with vibration alerts at mile marks"
- "a pendant that logs my breathing rate during meditation"

## Do NOT trigger when

- Pet collar / animal tracker → use `wondersmith-pet-collar`
- Medical-grade (SpO₂ diagnostic, clinical HR) → out of scope; regulatory cost is 10-100× this archetype's retail
- Smartwatch (full touchscreen, app store, LTE) → out of scope for wondersmith's pipeline
- Hearable / earbud → out of scope (acoustic + BT stack complexity)
- Running-shoe insole / footwear-embedded sensor → borderline; flag to user that shoe-embed adds IP68 and vibration specs

## How to invoke wondersmith

### Quick preview (standard, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 150
```

### Full design with CAD + skin-contact compliance (deliberate, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 150 \
  --detach
wondersmith runs watch <runId>
```

Use deliberate when the design includes a PPG / skin-touching sensor — the critic checks ISO 10993 biocompatibility.

## What wondersmith produces for this archetype

- BOM: ~18 components, raw $32-$65, quote-ready at 1.5× ($48-$98)
- Artifacts (deliberate/deep):
  - BOM.csv with BLE module + IMU MPNs
  - `concept.step`, `concept.stl`, `concept.glb` — body + strap
  - `concept.cadquery.py` — parametric body, strap attachment, charge-contact boss
  - `concept.kicad_sch` — nRF52 + IMU + haptic driver
  - `ORDER_INFO.json` — LeadArt plan, ~9 business days (overmolding silicone adds time)
- Suggested depth for this archetype: **deliberate** (fit + skin-contact matter more than in other archetypes)

## Prompt-writing tips for this archetype

Push the user to specify:

- **Wrist size** or adjustability (fixed single size, or 3 sizes S/M/L?)
- **Display** (single LED, 5×7 bar matrix, curved OLED — each is a big cost step)
- **Charging** (magnetic pogo, USB-C contact pad, or Qi wireless)
- **Water rating** ("just rain-proof" = IPX4, "swim-proof" = IPX7, "scuba" = IPX8+ out of scope)

If they mention heart-rate, ask whether they want real-time HR (expensive PPG + AFE) or just step-based activity (cheap IMU only).

## Reading the output

- **BOM.csv**: verify the BLE module is FCC-modular pre-certified (Raytac MDBT42Q, Nordic nRF52 SoM). If a bare SoC appears, flag the user — uncertified radio adds ~$8k + 8 weeks for intentional-radiator cert.
- **3D viewer**: strap attachment should be overmolded (single-part) or snap-fit with ≥50 N pull-out strength. Flag if strap uses glue.
- **Skin-contact material** in the final-report: polycarbonate + TPU is standard. Flag any ABS (cheaper but can cause mild skin reactions).

## Compliance heads-up

- **FCC Part 15 Class B** — every BLE wearable. Use a pre-certified module.
- **ISO 10993-5 & 10993-10** — cytotoxicity + sensitization for any material touching skin continuously. Required if PPG contacts skin.
- **IPX7** — 30 min submerged at 1 m. Default for wrist-worn; the deliberate run designs gaskets for this.
- **EN 62133** — LiPo cell safety. Use pre-certified cells, cheap cell vendors often lack this paperwork.
- **UN 38.3** — shipping lithium cells. Non-negotiable for international retail.

If the user wants to claim "heart rate monitoring" in marketing, the US FTC enforces truth-in-advertising — tell them PPG accuracy is ±5 BPM on a band this small and they should say "activity-based HR estimate" not "medical heart rate."
