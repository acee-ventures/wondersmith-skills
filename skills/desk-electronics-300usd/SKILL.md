---
name: desk-electronics-300usd
description: Production recipe for desk-class BLE/Wi-Fi consumer electronics targeting ~$250-300 retail. ESP32-class MCU, USB-C + LiPo power, 3D-printed PLA/PETG enclosure, LeadArt hand-assembly (~7 business days). Use when the prompt describes a battery-powered Wi-Fi/BLE desk gadget with plastic enclosure and retail target between $200 and $350.
license: CC-BY-NC-4.0
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
  coreComponents:
    - "ESP32-S3-WROOM-1 module or ESP32-C3 SuperMini (Wi-Fi + BLE)"
    - "USB-C receptacle + TP4056 LiPo charging IC + DW01 protection"
    - "LiPo 2000mAh 3.7V cell with thermal fuse"
    - "4.2-inch e-ink OR 2.4-inch TFT OR WS2812B addressable LED ring (pick one)"
    - "3D-printed PLA/PETG enclosure (~60-100 cm3, 2-part shell)"
    - "JST-PH 2-pin battery + JST-SH 4-pin sensor connectors"
    - "Tactile pushbuttons (1-3) + optional rotary encoder"
  safetyClass: consumer-electronics
  sourceRun: dd-1776448638477-w01q95
---

# Desk Electronics $300 Recipe

## When to use this skill
Trigger on prompts that combine:
1. Desk-class, counter-top, or bedside form factor (not wearable, not outdoor).
2. Wi-Fi or BLE connectivity is called out.
3. Retail price target in the $200-$350 range, or budget mentions suggest small-batch ≤ $120 raw BOM.
4. Battery-powered AND/OR USB-C powered (not mains, not industrial).
5. 3D-printed or lightweight plastic enclosure acceptable.

Do NOT use when: medical-grade, automotive-grade, food-contact, > 10W power draw, IP67+ rating required, or child-under-8 primary user (switch to `kids-toy` category).

## BOM skeleton (~22 components, $70-120 raw BOM → $105-180 quote-ready at 1.5x)

| Group | Representative part | Qty | Unit USD | Notes |
| --- | --- | --- | --- | --- |
| MCU module | ESP32-S3-WROOM-1-N8 | 1 | 4.20 | Drop-in ESP32-C3 SuperMini if BOM pressure |
| Sensor(s) | Task-specific (BME280, SCD40, VL53L0X, etc.) | 1-2 | 4-12 | Prefer I2C, 3.3V |
| Display | 4.2" e-ink GDEY042T81 | 1 | 18-22 | Swap for 2.4" TFT (~$8) if refresh rate matters |
| Power input | USB-C 6-pin receptacle | 1 | 0.45 | |
| Charging IC | TP4056 SOP-8 + 1kOhm Rprog | 1 | 0.40 | |
| Protection | DW01A + dual FET | 1 | 0.25 | Over-discharge / over-current |
| Battery | LiPo 3.7V 2000mAh 603450 + PTC fuse | 1 | 9.50 | |
| LDO | AMS1117-3.3 or MCP1700 | 1 | 0.20 | |
| Passives | Caps (10), resistors (8), LEDs (2) | ~20 | 0.02-0.10 | |
| Connectors | JST-PH 2-pin (battery) + JST-SH 4-pin (sensor) | 2 | 0.30 | |
| Input | 6x6 tactile switch | 1-3 | 0.15 | |
| Enclosure | PLA/PETG 3D print ~80 cm3 | 1 | ~11 | Volumetric rate $0.14/cm3 PLA |
| Hardware | M2.5 heat-set inserts + screws | 8 | 0.08 | |
| PCB | 2-layer 50x50mm prototype | 1 | 3-5 | JLCPCB small-batch |
| Labour | LeadArt hand-assembly | - | ~25 | Included in quote-ready multiplier |

**Target totals**: raw BOM $70-120, quote-ready (1.5x) $105-180, retail target $250-300.

## Manufacturing path
- LeadArt hand-assembly, **7 business days** typical turnaround.
- No SMT reflow needed when using MCU module + through-hole passives; hand-solder acceptable.
- PCB: JLCPCB 2-layer green, HASL finish, 50x50mm or smaller.
- 3D printing: PLA @ 0.2mm layer, 20% infill is sufficient for desk use. Volumetric cost: $0.14 per cm3 (include supports).
- Firmware: Arduino-ESP32 or ESP-IDF; OTA update via Wi-Fi.

## Safety checks (mandatory for audit)
- [ ] LiPo: thermal fuse + DW01 protection + JST-PH polarity key.
- [ ] USB-C: ESD TVS diode on VBUS + CC lines.
- [ ] Enclosure: sand 3D-print seams, no sharp edges, ventilation for charging IC heat.
- [ ] RF: ESP32 module is pre-certified FCC/IC (modular certification — cite module certification ID).
- [ ] CPSIA: only if shipping to under-12 users; otherwise consumer-electronics class.
- [ ] RoHS: specify RoHS-compliant passives from the start.

## Deviation guidance
- If prompt demands e-paper AND high refresh (> 0.5 Hz), flag as infeasible and recommend TFT instead.
- If prompt demands fanless sealed enclosure AND charging IC, add thermal relief holes or switch to lower-current charging (500 mA).
- If retail target < $200, drop ESP32-S3 to C3 SuperMini, drop e-ink to TFT, drop battery to 1000mAh — re-check BOM lands at $45-60 raw.
- If retail target > $350, upgrade to aluminium CNC enclosure, add Qi charging, or add second MCU for compute.
