---
name: wondersmith-kitchen-sensor
description: Use the `wondersmith` CLI to design a single-purpose countertop or handheld kitchen measurement device (temperature probes, scales, pH pens, humidity watchers) with a coin cell or AA, OLED or 7-seg readout, NSF food-contact materials where applicable, targeting $40-100 retail. Trigger when the user wants a small handheld sensor used near food or drink. Do NOT trigger for smart plugs, Wi-Fi meat thermometers, or any mains-powered appliance.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: kitchen-sensor
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 12
    bomMax: 28
    retailTarget: 80
  homepage: https://wondersmith.app
---

# wondersmith — kitchen sensor

## When to trigger this skill

The user's prompt should combine most of:

- **Purpose**: single-sensor measurement + readout (temp, weight, pH, humidity, hardness)
- **Context**: kitchen, bar, coffee, brewing, cooking, fridge, freezer
- **Power**: coin cell, single AA / AAA — weeks of standby, NOT continuously logging
- **Retail target**: $40-$100

Example prompts that match:

- "a BBQ meat thermometer with a K-type probe and an OLED"
- "a pour-over coffee scale accurate to 0.1 g"
- "a pH pen for sourdough starters"
- "a fridge-door hygrometer that beeps if humidity drifts"

## Do NOT trigger when

- Wi-Fi meat thermometer ≥$150 → use `wondersmith-desk-electronics`
- Smart plug or mains-powered appliance → out of scope
- Scale that holds >5 kg → industrial scale, different cert path
- Any product that stores food (insulated mug, sous-vide bath) → out of scope

## How to invoke wondersmith

### Quick preview (standard, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 80
```

### Full design with CAD + food-contact checklist (deliberate, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 80 \
  --detach
wondersmith runs watch <runId>
```

Use deliberate whenever the probe will touch food — the critic validates material choices against NSF/ANSI 51 and FDA 21 CFR 177.

## What wondersmith produces for this archetype

- BOM: ~10 components, raw $12-$28, quote-ready at 1.5× ($18-$42)
- Artifacts (deliberate/deep):
  - BOM.csv with sensor MPN + probe material spec
  - `concept.step`, `concept.stl`, `concept.glb` — housing + probe + button overmold
  - `concept.cadquery.py`
  - `ORDER_INFO.json` — LeadArt plan, ~6 business days (simplest archetype)
- Suggested depth for this archetype: **standard for tuning, deliberate for final** (food-contact certs need the critic pass)

## Prompt-writing tips for this archetype

Ask the user for:

- **Measurement target** ("0-300°C", "0.1 g at 2 kg max", "pH 2-12")
- **Probe contact** (does the sensor touch food directly, indirectly via glass, or only ambient?)
- **Display preference** (OLED if multi-line / graph, 7-seg if single value + cheap)
- **IP rating target** (IPX4 minimum for countertop, IPX5 if near sink)

Default to OLED unless they explicitly want cheap. Default to non-food-contact with a food-grade-adapter probe (easier cert).

## Reading the output

- **BOM.csv**: verify probe material row — stainless 304 / 316 is OK for food; aluminum is NOT; nickel-plated brass is questionable for FDA.
- **3D viewer**: silicone-overmolded buttons should be visible as distinct from the body. No mechanical button plunger reaches into the enclosure — liquid ingress.
- **`ORDER_INFO.json`**: LeadArt quotes 6 business days for this archetype. Faster than most.

## Compliance heads-up

- **NSF/ANSI 51** — required if the probe touches food directly. ~$500 cert lab fee per material.
- **FDA 21 CFR 177** — plastics contacting food >4 seconds. Most food-grade silicones (LSR) are pre-cleared; cheap TPE is not.
- **IPX4 minimum** — splash-resistant. The deliberate run will flag any USB port without a flap.
- **FCC Part 15** — only if the design has BLE (rare in this archetype — coin cells can't sustain Wi-Fi).
- **CE / UKCA** — self-declaration sufficient; no notified body for a low-voltage battery sensor.

If the user is brewing-adjacent (beer, wine, kombucha pH), you can optionally note the specific hobby communities have strong opinions on probe accuracy — tell wondersmith to spec a two-point calibration if the target is pH.
