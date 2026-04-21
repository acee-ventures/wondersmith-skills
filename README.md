# wondersmith-skills

Public design skill library used by the [WonderSmith](https://wondersmith.app) Deep Design pipeline.

Each `SKILL.md` follows the [agentskills.io](https://agentskills.io) format (flat YAML frontmatter + markdown body) so external agent frameworks — Claude Code, Cursor, Codex, Hermes — can consume them directly.

## What's in a skill

Each file encodes a **production recipe**: a budget band, core component list, realistic per-unit pilot cost sketch, and the compliance items (CPSIA / IPX rating / NSF / UL / FCC SDoC) that LLMs tend to miss without guidance. The skills move domain facts out of the prompt context.

## Current skills (11)

| File | Retail | Category |
|---|---|---|
| `desk-electronics-300usd` | $300 | desk-electronics — ESP32 + USB-C + LiPo + PLA enclosure |
| `kids-toy-soft-100usd` | $100 | kids-toy — plush + ATtiny + CPSIA/ASTM F963 |
| `wearable-fitness-150usd` | $150 | wearable — nRF52 + IMU + haptic + IPX7 |
| `kitchen-sensor-80usd` | $80 | kitchen-sensor — single-purpose + NSF probe |
| `desk-lamp-120usd` | $120 | lighting — CRI≥90 COB + UL 153 |
| `bike-accessory-90usd` | $90 | bike-accessory — LiPo + IPX7 + vibration-tolerant |
| `pet-collar-70usd` | $70 | pet-wearable — chew-resistant BLE |
| `audio-speaker-180usd` | $180 | audio — BT 5.2 LE Audio + Class-D |
| `led-wall-display-200usd` | $200 | led-display — WS2812 + ESP32 + WLED |
| `battery-tool-150usd` | $150 | power-tool — 18650 pack + UL 62841 |
| `plant-care-device-110usd` | $110 | plant-care — soil/light + ESP32-C3 |

## How to use

### From a WonderSmith run
Skills are matched automatically when a design prompt's inferred category + budget band overlap. Matched skills are injected into Multi-Agent and Deep Design system prompts as prior-art context.

### From another framework
Drop any `SKILL.md` into your agent's skill directory. The frontmatter is the canonical agentskills.io shape:

```yaml
---
name: desk-electronics-300usd
description: <one-sentence trigger>
license: proprietary
compatibility:
  agentskills: "1.0"
metadata:
  category: desk-electronics
  budgetBand:
    bomMin: 70
    bomMax: 120
    retailTarget: 300
  manufacturingDays: 7
  componentCount: 22
  coreComponents: [...]
  safetyClass: consumer-electronics
---
```

## Contributing

New skills are auto-extracted from successful Deep Design runs (audit pass + BOM + final-report + concept-image all present) and deduped at Jaccard ≥ 0.85 on token overlap. Manual PRs welcome if you have a retail bracket not yet covered.

## License

Each skill specifies its own `license` field in frontmatter. WonderSmith-authored skills are released under **[CC-BY-NC-4.0](https://creativecommons.org/licenses/by-nc/4.0/)**: you may share and adapt them freely for non-commercial use, with attribution to WonderSmith / acee-ventures. For commercial redistribution or re-licensing, contact us.

The root `LICENSE` file applies to the repository as a whole; individual skills retain whatever license their frontmatter declares.

## Source

These skills are mirrored from the private [wondersmith-app](https://github.com/acee-ventures/wondersmith-app/tree/main/skills/design) repo whenever they land on `main`.
