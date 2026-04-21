---
name: wondersmith-pet-collar
description: Use the `wondersmith` CLI to design a dog / cat collar-mounted tracker, ID tag, or activity monitor (BLE beacon, chew-resistant polycarbonate, magnetic pogo charging, no exposed batteries, IPX6) targeting $25-110 retail. Trigger when the user wants a clip-on or collar-threaded wearable for pets. Do NOT trigger for GPS trackers (LTE-M complexity), pet cameras, or any product with exposed fasteners.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: pet-wearable
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 10
    bomMax: 22
    retailTarget: 70
  homepage: https://wondersmith.app
---

# wondersmith — pet collar tracker

## When to trigger this skill

The user's prompt should combine at least two of:

- **Form factor**: attaches to a dog or cat collar (clip / thread / D-ring loop)
- **Pet type**: dog, cat, small pet (rabbit, ferret on larger units)
- **Purpose**: BLE presence, activity count, ID tag with light, lost-pet beacon
- **Retail target**: $25-$110

Example prompts that match:

- "a glow-in-the-dark BLE ID tag that pings my phone if my cat leaves the yard"
- "a dog activity tracker that logs walks and rest"
- "a light-up safety tag for night walks"
- "a chewable-rated collar beacon for a rescue organization"

## Do NOT trigger when

- GPS tracker with cellular (LTE-M, NB-IoT) → out of scope; requires carrier cert + monthly data plan
- Pet camera / feeder → use `wondersmith-desk-electronics` or decline
- Shock collar / training device → wondersmith does not design aversive products
- Harness / chest strap → different form factor; flag to user and ask if collar-only works

## How to invoke wondersmith

### Quick preview (standard, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 70
```

### Full design with CAD + chew / swallow validation (deliberate, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 70 \
  --detach
wondersmith runs watch <runId>
```

Always deliberate for this archetype — pet-safety features (no exposed batteries, chew-test pass, D-ring pull strength) are load-bearing and the critic catches them.

## What wondersmith produces for this archetype

- BOM: ~11 components, raw $10-$22, quote-ready at 1.5× ($15-$33)
- Artifacts (deliberate/deep):
  - BOM.csv with sealed-battery + magnetic-charge spec
  - `concept.step`, `concept.stl`, `concept.glb` — welded body + D-ring
  - `concept.cadquery.py` — snap-fit + ultrasonic weld groove
  - `ORDER_INFO.json` — LeadArt plan, ~7 business days
- Suggested depth for this archetype: **deliberate** (safety specs are non-negotiable)

## Prompt-writing tips for this archetype

Ask the user:

- **Pet size range** (small cat 2 kg / medium dog 10 kg / large dog 30 kg) — affects unit weight ceiling (≤5% of body weight rule of thumb)
- **Daily activity level** (indoor / suburban walks / hiking / working dog)
- **Water exposure** (river swims = IPX7; rain only = IPX4-6)
- **Visibility requirement** (bright LED for night? fully hidden so it doesn't snag?)

Default to IPX6 minimum (dogs swim unpredictably) and BLE-only (no LTE / no GPS at this tier).

## Reading the output

- **BOM.csv**: MUST NOT contain CR2032 / CR2025 or any coin cell. Must use a sealed LiPo with no user-accessible access. Flag immediately if coin cells appear.
- **3D viewer**: enclosure should be ultrasonic-welded, not screwed. No Phillips or hex heads visible. D-ring clip metal should be stainless 304 (non-nickel — some pets are nickel-allergic).
- **Final-report**: "chew test 50N bite force for 30s" should appear in QA plan. Flag absence.
- **Color note**: if the user wants a bright LED, the shell cannot be black or dark green (absorbs light). Tell wondersmith to spec translucent polycarbonate with a pigmented clip.

## Compliance heads-up

- **Pet industry chew test** — 50 N bite force, 30 s, no enclosure crack. Not a formal cert, but retail buyers test-sample.
- **Swallow-safety** — no part <15 mm on any axis (choking hazard for medium+ breeds). The critic checks part dimensions.
- **Battery enclosure drop test** — 3 m onto concrete, no cell exposure. Critical for lost-collar recovery scenarios.
- **IPX6 minimum** — dog shakes are violent; IPX6 tolerates high-pressure spray which maps to shake-off.
- **FCC Part 15 Class B** — any BLE radio. Use pre-certified nRF52 module (preferably nRF52805 — smallest package at this tier).
- **EU pet product** — no specific pet-electronics directive, but CE marking via low-voltage self-declaration applies.

Don't claim "GPS" in marketing unless you have a real GNSS chip — the pet-owner community is specifically burned on "GPS trackers" that are just BLE.
