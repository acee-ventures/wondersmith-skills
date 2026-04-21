---
name: kids-toy-soft-100usd
description: Production recipe for soft, battery-powered children's toys (ages 3-8) targeting ~$60-120 retail. Plush exterior over 3D-printed ABS skeleton, ATtiny / Seeeduino XIAO MCU, low-current haptics + LEDs, single AA/AAA cells with access door. Use when the prompt describes a plush/soft tactile toy for kids with light, sound, or vibration, retail target between $40 and $130, and strict CPSIA / ASTM F963 compliance.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: kids-toy
  budgetBand:
    bomMin: 18
    bomMax: 45
    retailTarget: 100
  manufacturingDays: 10
  componentCount: 14
  coreComponents:
    - "ATtiny85 OR Seeeduino XIAO M0 (no Wi-Fi; Bluetooth optional via RN4870 module)"
    - "Piezo buzzer or 8Ω 0.5W speaker with spam filter capacitor"
    - "3mm LEDs ×3-6 behind diffuser, current-limited via 220Ω resistors"
    - "Tactile capacitive touch pad or pressure switch (no exposed metal contacts)"
    - "Plush exterior: 100% polyester fiberfill + child-safe embroidered eyes (NO glued buttons)"
    - "3D-printed ABS internal skeleton, rounded corners ≥3mm radius"
    - "1× or 2× AAA NiMH in isolated compartment with Phillips-screw door"
    - "TP4054 or equivalent coin-cell LiPo only if rechargeable; NOT loose cells"
  safetyClass: kids-toy-ce-astm
  complianceMust:
    - "CPSIA Phase 2 lead content <100ppm (verified via XRF at sampling)"
    - "ASTM F963-17 small parts cylinder for ages <3"
    - "Battery door requires coin + 1/4 turn OR 2 simultaneous actions (ASTM F963-17 sec 4.25)"
    - "Sound pressure ≤85dBA at 25cm (ASTM F963 sec 4.5)"
    - "EN71-3 migration of chemical elements for EU export"
  sourceRun: null
---

# Kids Soft-Toy $100 Recipe

## When to use this skill

Trigger on prompts combining ≥2 of:
- plush / stuffed / soft / cuddly / hug exterior
- kids / children / toddler / nursery audience
- gentle light, sound, haptic feedback
- retail $40-$130

## Hard constraints (safety > features)

1. **No loose button-cell cells** — if coin cell needed, must be soldered-in LiPo. CPSC has zero tolerance for child-accessible button cells.
2. **Battery door**: two-action open (screw + push) OR coin-slot screw.
3. **No exposed conductors** within reach. All pads potted in silicone or recessed ≥3mm.
4. **Round external geometry** ≥3mm radius corners; no small parts for ages <3 (use the small-parts cylinder test gauge).
5. **No external antennas, sharp finials, or pull-off eyes**. Eyes embroidered, not plastic-button-glued.

## Assembly notes (LeadArt)

- Plush shell sourced from Shenzhen Haishunda or equivalent ISO 9001 shop (NOT AliExpress generic).
- 3D-printed skeleton pre-sanitized via IPA wipe before final insert.
- Functional burn-in: 8h continuous LED + sound at max PWM before QA sign-off.
- Label: "Ages 3+", CE mark, UKCA (post-Brexit), battery warning pictogram.

## Cost sketch (22 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| ATtiny85 + passives | 22 | $1.80 | $39.60 |
| Piezo buzzer | 22 | $0.90 | $19.80 |
| LEDs + resistors | 132 | $0.08 | $10.56 |
| Capacitive touch IC | 22 | $1.10 | $24.20 |
| Plush shell + fiberfill | 22 | $9.50 | $209.00 |
| 3D-printed skeleton | 22 | $3.20 | $70.40 |
| Batteries + door hardware | 22 | $1.40 | $30.80 |
| QA + compliance lab fee (amortized) | 1 | $180 | $180.00 |
| **Total** | | | **~$584** |
| Per-unit landed | | | **~$27** |

Retail $80-120 leaves ~3-4× multiplier margin, standard for specialty kids toys.
