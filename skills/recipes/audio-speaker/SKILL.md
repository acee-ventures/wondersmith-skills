---
name: wondersmith-audio-speaker
description: Use the `wondersmith` CLI to design a portable or bookshelf Bluetooth speaker (BLE 5.2 / LE Audio, Class-D amp, single-driver or 2-way, LiFePO4 cell, injection-molded ABS + perforated steel grille) targeting $80-260 retail. Trigger when the user asks for a wireless speaker, portable boombox, or small bookshelf audio unit. Do NOT trigger for TV soundbars (latency requires HDMI ARC or LC3), hearables, or studio monitors.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: audio
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 38
    bomMax: 85
    retailTarget: 180
  homepage: https://wondersmith.app
---

# wondersmith — portable audio speaker

## When to trigger this skill

The user's prompt should combine most of:

- **Product**: wireless speaker / portable boombox / bookshelf audio unit
- **Connectivity**: Bluetooth pair (not Wi-Fi streaming — that's a different archetype and stack)
- **Form factor**: handheld to bookshelf; not in-wall, not car audio
- **Retail target**: $80-$260

Example prompts that match:

- "a portable camping speaker with warm sound and 12 hr battery"
- "a bookshelf speaker for my kitchen that pairs to multiple phones"
- "a pocket Bluetooth speaker with surprisingly good bass for its size"
- "a rugged jobsite speaker rated for dust and splashes"

## Do NOT trigger when

- TV soundbar → A2DP latency (150-200 ms) breaks lip-sync. Needs LE Audio LC3 or wired HDMI ARC; out of scope for this archetype.
- Earbuds / headphones → hearables are a separate product class with TWS stack complexity
- Studio monitor / reference speaker → acoustic engineering beyond this pipeline's depth
- Smart speaker with voice assistant → AI stack + mic array complexity out of scope
- Car audio / amplifier-in-a-box → different cert path (DIN plug, marine ratings)

## How to invoke wondersmith

### Quick preview (standard, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 180
```

### Full design with CAD + acoustic + cert plan (deliberate, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 180 \
  --detach
wondersmith runs watch <runId>
```

Deliberate is recommended — enclosure volume vs driver Thiele-Small parameters actually matter for sound quality; the critic checks the math.

## What wondersmith produces for this archetype

- BOM: ~16 components, raw $38-$85, quote-ready at 1.5× ($57-$128)
- Artifacts (deliberate/deep):
  - BOM.csv with driver MPN, amp IC, BT module
  - `concept.step`, `concept.stl`, `concept.glb` — body + grille + passive radiator
  - `concept.cadquery.py` — parametric enclosure volume + grille perforation
  - `concept.kicad_sch` — amp + BT + charger
  - `ORDER_INFO.json` — LeadArt plan, ~8 business days
- Suggested depth for this archetype: **deliberate** (acoustic math + BT-SIG cert planning are non-trivial)

## Prompt-writing tips for this archetype

Push the user for:

- **Target sound profile** ("warm + bass-forward", "reference-flat", "voice clarity for podcasts")
- **Battery life** ("12 hr at moderate volume" → sizes the cell)
- **IP rating** ("splash-resistant" = IPX4, "jobsite" = IPX5-6, "poolside" = IPX7)
- **Driver size preference** (2" full-range for pocket, 3" + passive radiator for bookshelf)

Default to BLE 5.2 with LE Audio if the user mentions "low latency". Use QCC30xx Qualcomm SoC if they want aptX HD; ESP32-S3 audio if they want cheap.

## Reading the output

- **BOM.csv**: verify BT IC is BT-SIG qualified (has a Declaration ID). Most pre-made modules are; bare SoCs often require ~$10k BT-SIG fee on top of cert.
- **3D viewer**: check enclosure volume in the parametric source — a 3" driver needs 1-2 L for decent low end. If the shell is sub-500 mL with a 3" driver, sound will be boxy.
- **Battery**: LiFePO4 preferred over LiPo for this archetype (longer cycle life, safer hot-car scenarios).

## Compliance heads-up

- **FCC Part 15 Class B + SDoC** — every BT device. Use pre-certified BT module.
- **BT-SIG Declaration ID** — legally required to use the Bluetooth logo on packaging. Pre-certified modules often cover this; bare-SoC designs require paying BT-SIG directly.
- **EN 50332-1** — max SPL for portable audio (EU). Consumer speakers typically exempt, but if marketed for kids the cap is stricter.
- **UN 38.3** — LiFePO4 / LiPo shipping.
- **FCC Part 15 Subpart C** — if the design includes an FM aux input or any other intentional radiator.
- **CE / UKCA** — self-declaration for the EMC + radio; notified body usually not required.

The audio community cares about latency and driver match. Tell wondersmith to include the measured Thiele-Small parameters in the final-report, not just the driver MPN.
