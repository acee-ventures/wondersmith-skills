---
name: desk-lamp-120usd
description: Production recipe for desk lamps and accent lights targeting ~$80-180 retail. High-CRI LED bar or circular panel, 5V USB-C wall-wart input, PWM + color-temp dim via capacitive touch or rotary encoder, anodized aluminum + PLA/PETG shade. Use when the prompt describes a desk / bedside / shelf lamp with dimming or color tunability, retail $60-$200, no smart-home connectivity beyond optional BLE.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: lighting
  budgetBand:
    bomMin: 22
    bomMax: 48
    retailTarget: 120
  manufacturingDays: 7
  componentCount: 13
  coreComponents:
    - "Warm+cool dual-channel COB LED strip (CRI≥90, 2700K+6500K)"
    - "MP1584 or TPS5430 buck to 5V→ LED Vf (most COBs are 5V or 6V)"
    - "ATtiny1616 / STM8S for PWM dim + touch (no Wi-Fi at this tier)"
    - "Capacitive touch pad (TTP223) or KY-040 rotary encoder w/ detent"
    - "USB-C PD-compliant input (PD negotiated 5V/3A); no barrel jacks"
    - "Extruded aluminum heat-spreader on bar lamps; thermal pad to shell"
    - "Anodized aluminum + 3D-printed PLA/PETG base (weighted with ≥200g steel puck)"
  safetyClass: class-III-lv-lighting
  complianceMust:
    - "UL 153 portable lamps (US) OR EN 60598-2-4 (EU) depending on market"
    - "Photobiological safety IEC 62471 (exempt group for <400 lumens at 20cm)"
    - "Energy Star 2.0 if claiming energy efficiency"
    - "FCC Part 15 class B (PWM can radiate EMI at 30-300kHz; needs filtering)"
  sourceRun: null
---

# Desk Lamp $120 Recipe

## Heat management (most common failure mode)

- COB LED operating at 3-4W must dissipate to aluminum spreader ≥8 cm² / W.
- Thermal pad between COB and aluminum — NOT thermal paste (vibration migrates it).
- Never fully enclose COB in 3D-printed plastic; PLA softens at 60°C, PETG at 75°C.

## Dimming curve

Human eye is logarithmic. Linear PWM feels harsh. Use CIE 1931 L* curve:
```c
duty = pow(slider/255.0, 2.5) * 255;
```

## Cost sketch (30 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| COB dual-CCT strip | 30 | $4.80 | $144 |
| Buck + MCU + driver | 30 | $3.20 | $96 |
| USB-C input module | 30 | $1.60 | $48 |
| Touch/encoder | 30 | $1.10 | $33 |
| Aluminum extrusion | 30 | $5.60 | $168 |
| 3D-printed base | 30 | $3.20 | $96 |
| Steel counterweight | 30 | $1.80 | $54 |
| Assembly + cert | 30 | $2.40 | $72 |
| **Total** | | | **~$711** |
| Per-unit landed | | | **~$24** |
