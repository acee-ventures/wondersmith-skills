---
name: wondersmith-kids-toy-soft
description: Use the `wondersmith` CLI to design a plush / soft children's toy for ages 3-8 with battery-powered lights, sound, or vibration (ATtiny / XIAO-class MCU, AA/AAA isolated compartment, strict CPSIA / ASTM F963 compliance) targeting $40-130 retail. Trigger when the user asks for a stuffed, cuddly, tactile interactive toy for kids. Do NOT trigger for collectibles, adult plush, electronics-only kids gadgets, or anything with exposed small parts.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: kids-toy
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 18
    bomMax: 45
    retailTarget: 100
  homepage: https://wondersmith.app
---

# wondersmith — soft kids toy

## When to trigger this skill

The user's prompt should combine at least two of:

- **Form factor**: plush / stuffed / soft / cuddly / squishy / huggable exterior
- **Audience**: kids / children / toddler / preschool / nursery (ages roughly 3-8)
- **Interaction**: lights / sound / haptics / gentle sensors (tactile, capacitive, pressure)
- **Retail target**: $40-$130

Example prompts that match:

- "a plush owl that plays lullabies and has glowing eyes"
- "a squishable bunny that vibrates gently when you squeeze it"
- "a bedtime bear that reads a pre-recorded story when you press its paw"
- "a nursery nightlight hidden inside a soft cloud"

## Do NOT trigger when

- Adult collectible plush (no electronics, or under-3 collector display) → not a wondersmith use case
- Electronic kids gadget without plush exterior → use `wondersmith-desk-electronics`
- Ride-on toy, powered vehicle, drone, anything with motors → out of scope
- Age <3 primary user → advise the user that ASTM F963 small-parts cylinder rules forbid most of what this archetype includes. Default to ages 3+ and tell them explicitly.
- Outdoor-only toy → use `wondersmith-bike-accessory` or decline

## How to invoke wondersmith

### Quick preview (standard, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 100
```

Use for rapid BOM + concept. Does NOT validate compliance.

### Full design with CAD + compliance checklist (deliberate, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 100 \
  --detach
wondersmith runs watch <runId>
```

**Deliberate is strongly recommended for this archetype.** Compliance for children's products is not negotiable — the critic pass checks battery-door retention, small-parts cylinder, and sound pressure limits.

## What wondersmith produces for this archetype

- BOM: ~14 components, raw $18-$45, quote-ready at 1.5× ($27-$68)
- Artifacts (deliberate/deep):
  - BOM.csv with child-safe MPN choices
  - `concept.step`, `concept.stl`, `concept.glb` — internal ABS skeleton + battery compartment
  - `concept.cadquery.py` — parametric skeleton + compartment
  - `ORDER_INFO.json` — LeadArt plan, ~10 business days (plush is slower than rigid)
  - Compliance checklist artifact calling out CPSIA / ASTM / EN 71-3 specifics
- Suggested depth for this archetype: **deliberate** (compliance is mandatory)

## Prompt-writing tips for this archetype

Encourage the user to specify:

- **Age range** ("ages 3+", "ages 5-8") — affects small-parts limits
- **Plush size** ("roughly the size of a throwaway tissue box", "fits in a backpack")
- **Sound requirement** (songs, story, reactive effects, or no sound)
- **Wash-ability** (fully removable electronics or sealed-in?)

If they're vague on age, default to 3+ and tell them that's the lowest supported age for any electronic soft toy under this skill.

## Reading the output

- **BOM.csv**: check for `CR-2025 / CR-2032` coin cells → REJECT. This archetype uses AA/AAA in a screwed-shut compartment only. Coin cells are a swallowing hazard and banned by ASTM F963 without adult-actuated access.
- **3D viewer**: verify the battery door has the "coin + 1/4 turn" or "2 simultaneous action" mechanism on the STEP file.
- **Compliance section**: if EN 71-3 is called out, the plush fill must be Oeko-Tex Standard 100. Flag to user if BOM line says "polyester fiberfill" without the Oeko-Tex note.

## Compliance heads-up

These are not optional for retail sale:

- **CPSIA Phase 2** — lead <100 ppm (verified by XRF at sampling). Applies to every painted or metal-containing part.
- **ASTM F963-17** — the primary US toy safety standard. Includes small-parts cylinder, sharp-point/edge tests, sound pressure ≤85 dBA at 25 cm, battery-door retention.
- **EN 71-3** — migration of chemical elements (EU). If the user wants EU distribution, flag early — it's a separate test cycle ~$200.
- **FCC Part 15 Class B** — only if the design has a radio. Most kids-toy-soft designs avoid BLE to keep cert cost low.
- **Tracking label** — CPSIA requires a manufacturer label with batch/date so parents can look up recalls. The LeadArt order plan includes this by default.

Tell the user up front: compliance testing typically adds $150-$300 per archetype to the pilot budget. That's not reflected in the raw BOM — it's a cert lab fee amortized across the first 20-50 units.
