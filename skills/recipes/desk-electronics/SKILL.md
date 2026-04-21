---
name: wondersmith-desk-electronics
description: Use the `wondersmith` CLI to design a battery-powered Wi-Fi/BLE desk-class consumer electronics product (ESP32-class, USB-C + LiPo, 3D-printed enclosure) targeting $200-350 retail. Trigger when the user asks for a small desk / counter / bedside gadget with Wi-Fi or Bluetooth, plastic enclosure, rechargeable battery, and a retail price in the low-to-mid hundreds. Do NOT trigger for wearables, outdoor gear, medical devices, or anything over 10W power draw.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: desk-electronics
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 70
    bomMax: 120
    retailTarget: 300
  homepage: https://wondersmith.app
---

# wondersmith — desk electronics

## When to trigger this skill

The user's prompt should combine most of:

- **Form factor**: desk / counter / shelf / bedside / nightstand
- **Connectivity**: Wi-Fi OR Bluetooth (BLE or BT classic) is mentioned or clearly implied
- **Power**: USB-C powered AND/OR has a rechargeable battery; not mains-only, not >10W
- **Enclosure**: plastic / 3D-printed / injection-molded acceptable; user is not asking for machined aluminum or glass
- **Retail target**: $200-$350 range (or budget mentions raw BOM under $120)

Example prompts that match:

- "a desk clock with an e-ink face and weather sync"
- "a BLE environment monitor that sits next to my monitor and shows CO₂"
- "a small pomodoro timer with an LED ring and a rotary encoder"
- "a status cube for my home office that mirrors my Slack DND state"

## Do NOT trigger when

- Wearable / body-worn → use `wondersmith-wearable-fitness` or `wondersmith-pet-collar`
- Outdoor / weatherproof → use `wondersmith-bike-accessory`
- Food-contact / countertop sensor → use `wondersmith-kitchen-sensor`
- Lamp as the primary purpose → use `wondersmith-desk-lamp`
- Speaker / audio as the primary purpose → use `wondersmith-audio-speaker`
- Child under 8 as primary user → use `wondersmith-kids-toy-soft`
- Any claim of medical, automotive, or IP67+ rating → out of scope for this archetype

## How to invoke wondersmith

### Quick preview (standard depth, ~3 min, OK to block)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 300
```

Use this when the user just wants a sanity-check BOM and a one-paragraph concept. No CAD files, no critic-audit pass.

### Full design with CAD files (deliberate depth, ~15 min, MUST detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 300 \
  --detach
# returns a runId. Then:
wondersmith runs watch <runId>
```

Use this when the user wants actual `.step` / `.stl` / `.glb` / `cadquery.py` / `kicad_sch` artifacts. Deliberate runs go through the critic loop (exec + LLM + text) with up to 2 retry iterations.

**Never block the user's conversation for a deliberate run.** Always `--detach`, return the runId, and resume polling on the next turn.

## What wondersmith produces for this archetype

- BOM: ~22 components, raw $70-$120, quote-ready at 1.5× safety ($105-$180)
- Artifacts (deliberate/deep tiers only):
  - `BOM.csv` — parts list with MPN + supplier hints
  - `concept.step`, `concept.stl`, `concept.glb` — solid model, printable, viewable
  - `concept.cadquery.py` — parametric source (editable in VSCode or CQ-editor)
  - `concept.kicad_sch` — schematic capture starting point
  - `ORDER_INFO.json` — LeadArt hand-assembly plan, ~7 business days
- Typical Deep Design wall-clock: 7-12 min standard, 12-20 min deliberate
- Suggested depth for this archetype: **deliberate** (users buying desk electronics usually want to see the CAD before committing)

## Prompt-writing tips for this archetype

Encourage the user to include:

- **Dimensions** or volume hint ("fits in my palm", "footprint of a hockey puck")
- **Display type** if they care (e-ink for always-on, TFT for color, LED ring for ambience)
- **Battery life expectation** ("runs a week on a charge", "always plugged in")
- **Visual style** ("matte black, no logo", "warm wood + brass accents")

If they don't mention battery, assume USB-C powered with a fallback 2000mAh LiPo. If they mention "always on", lean e-ink over TFT.

## Reading the output

- **BOM.csv**: check `raw_total_usd` vs `quote_ready_total_usd`. Quote-ready includes LeadArt's safety factor (typically 1.5×). Both are per-unit, not batch.
- **3D viewer URL**: every run returns a `/p/<id>/visuals` link. Show the user the GLB preview first — it's the single most useful artifact for approval.
- **`ORDER_INFO.json`**: the `manufacturingDays` field is the promise. For desk-electronics at 22 components LeadArt typically quotes 7 business days from order-confirm.

## Compliance heads-up

Set user expectations up front — the deliberate run will flag these, but mentioning them early saves cycles:

- **FCC Part 15 Class B** — any Wi-Fi or BLE radio. Cheapest path: use a pre-certified module (ESP32-WROOM-32U, Raytac MDBT42Q).
- **UN 38.3** — required for shipping any device with a LiPo cell internationally.
- **RoHS 3 / REACH SVHC** — EU market requirement. Most modern ICs are compliant; flag if the design includes leaded solder or legacy connectors.
- **CE / UKCA self-declaration** — sufficient for this archetype if you use pre-certified radio modules. No notified body needed.

For any child-accessible unit (<8), switch to the `wondersmith-kids-toy-soft` skill instead.
