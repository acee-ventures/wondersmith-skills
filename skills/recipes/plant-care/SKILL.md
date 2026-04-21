---
name: wondersmith-plant-care
description: Use the `wondersmith` CLI to design a houseplant care device (soil moisture + light meters, self-watering valves, BLE plant monitors, small grow-light arrays) with capacitive soil probe, BH1750 light, HDC2080 temp/humidity, LiPo + optional solar, IP67 potted probe tip, targeting $40-180 retail. Trigger when the user wants something that helps keep houseplants alive. Do NOT trigger for commercial agriculture, hydroponic kits, or outdoor garden systems.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: plant-care
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 16
    bomMax: 38
    retailTarget: 110
  homepage: https://wondersmith.app
---

# wondersmith — plant care device

## When to trigger this skill

The user's prompt should combine most of:

- **Target**: houseplants / indoor gardening / bonsai / small planter setups
- **Sensing**: soil moisture, light, ambient temp/humidity, or water level
- **Actuation** (optional): LED grow array, solenoid valve for self-watering
- **Connectivity**: BLE or Wi-Fi for phone/app sync (optional)
- **Retail target**: $40-$180

Example prompts that match:

- "a soil moisture meter that sticks in the pot and beeps when dry"
- "a BLE plant monitor with a phone app and water reminders"
- "a self-watering valve for a 2-gallon planter"
- "a small LED grow array for a windowsill herb garden"

## Do NOT trigger when

- Commercial agriculture / farm-scale → industrial sensing is out of scope
- Hydroponics kit / NFT channels → different plumbing + cert path
- Outdoor irrigation controller → outdoor-weatherproof + mains-power complexity
- Full greenhouse HVAC → out of scope
- Grow tent with HID lighting → electrical + fire risk beyond this archetype

## How to invoke wondersmith

### Quick preview (standard, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 110
```

### Full design with CAD + soil-contact materials (deliberate, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 110 \
  --detach
wondersmith runs watch <runId>
```

Deliberate is recommended if the probe is buried — soil chemistry is surprisingly harsh on cheap materials; the critic validates.

## What wondersmith produces for this archetype

- BOM: ~14 components, raw $16-$38, quote-ready at 1.5× ($24-$57)
- Artifacts (deliberate/deep):
  - BOM.csv with sensor array MPN + ASA probe stem
  - `concept.step`, `concept.stl`, `concept.glb` — body + probe + charging pad
  - `concept.cadquery.py` — parametric body with probe insertion depth
  - `concept.kicad_sch` — ESP32 + sensors + battery management
  - `ORDER_INFO.json` — LeadArt plan, ~7 business days
- Suggested depth for this archetype: **deliberate** when the probe contacts soil

## Prompt-writing tips for this archetype

Ask the user:

- **Plant type** (succulent = dry-tolerant, fern = moist-loving — affects moisture alert thresholds)
- **Indoor vs. windowsill** (windowsill sees direct UV through glass — ASA/PC shell required; pure PLA cracks in ~3 months)
- **Self-watering actuation?** ("just measure" = cheap; "water when dry" = adds valve + reservoir)
- **App integration** (Home Assistant, Plant-Care-specific apps, or standalone)

Default to capacitive soil probe (NEVER resistive — they corrode in days under soil). Default to BLE for sync (Wi-Fi eats battery too fast for a weeks-standby product).

## Reading the output

- **BOM.csv**: verify soil probe is **capacitive**, not resistive. Verify probe stem material is **ASA or PC**, not PLA. Flag both immediately if wrong.
- **3D viewer**: probe tip potting should be visible as a distinct region. Potting compound should be epoxy (NOT silicone — soil chemistry attacks silicone).
- **Final-report**: if the design claims grow-light output, lumens should match the plant's DLI (daily light integral) target. Generic "full spectrum white LED" is too vague — spec the PPFD at expected distance.

## Compliance heads-up

- **FCC Part 15 Class B** — BLE or Wi-Fi radio.
- **UL 94 V-0** — flame-resistant potting, required if near any grow-light heat source.
- **Lead-free solder on probe tip** — plant-contact (even indirect) rules.
- **EN 60335-1** — general safety for low-voltage household appliances (EU market).
- **CE / UKCA** — self-declaration.
- **Water exposure** — the probe tip is IP67 (potted); the body housing is IPX4 minimum (water splash from careless watering).

If the user wants "smart plant care" language, tell wondersmith to include: soil moisture % mapped to plant-type-specific thresholds (succulent: 10-20%; tropical: 40-60%), light DLI computed over 24 hours, and a 7-day trend graph. Generic "needs water" / "needs light" alerts are uninformative.
