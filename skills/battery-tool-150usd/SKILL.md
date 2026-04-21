---
name: battery-tool-150usd
description: Production recipe for cordless battery-powered hand tools targeting ~$90-200 retail — mini circular saws, sanders, screwdrivers, rotary tools, heat guns. 18650/21700 pack, BLDC motor or brush motor depending on duty cycle, trigger + lock switch, overcurrent protect. Use when the prompt describes a hand-held power tool for DIY / hobbyist / light-duty use, retail $70-250.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: power-tool
  budgetBand:
    bomMin: 38
    bomMax: 80
    retailTarget: 150
  manufacturingDays: 10
  componentCount: 18
  coreComponents:
    - "BLDC motor (e.g. RS550, 12V, 15kRPM no-load) OR 775 brush motor for cost-sensitive tier"
    - "ESC module or simple MOSFET H-bridge (STP75NF75 class) for brush motor"
    - "2S or 3S 18650 pack (4000-6000mAh) with BMS including cell balancing"
    - "TP5100 + protection OR dedicated 2-stage BMS (CC→CV + cell-balance)"
    - "Trigger switch with variable PWM (Hall-effect sensor, NOT rheostat — they burn out)"
    - "Thermal cutoff: PTC thermistor on motor casing, trips at 90°C"
    - "Glass-filled nylon body (PA6-GF30) + overmolded TPE grip"
  safetyClass: power-tool-UL62841
  complianceMust:
    - "UL 62841 or EN 62841 (handheld power tool safety)"
    - "Double insulation marking OR Class I with proper ground path"
    - "Overcurrent protection (must trip within 100ms at 2× rated current)"
    - "FCC Part 15 class B (brushed motors radiate, need ferrite suppression)"
    - "UN 38.3 for Li-ion pack shipping"
    - "Conductive dust exclusion IPX5 on vent (for sanders especially)"
  sourceRun: null
---

# Battery Hand Tool $150 Recipe

## Motor sizing

| Use | No-load RPM | Stall torque | Typical motor |
|---|---|---|---|
| Rotary tool / Dremel | 15-25k | 1-3 Ncm | RS550 12V BLDC |
| Driver / screwdriver | 500-2k (geared) | 10-30 Ncm | 775 brush + 20:1 planetary |
| Circular saw (mini) | 3-5k | 5-15 Ncm (at spindle) | 775 brush 18V + belt reduction |
| Heat gun (fan) | 10-15k | low (pushing air) | 550-class brush |

Don't spec BLDC for ≤$150 unless the target is rotary tool. Brush motors + basic PWM carry the $70-150 segment.

## Thermal cutoff placement

PTC on motor housing, NOT on PCB — PCB temp lags motor by 20-30s under overload, by which time brushes are already gone.

## Cost sketch (40 unit pilot)

| Line | Qty | Unit | Total |
|---|---|---|---|
| 775 brush motor + planetary | 40 | $7.40 | $296 |
| 2S 18650 pack (2×4000mAh) + BMS | 40 | $11.20 | $448 |
| MOSFET driver + PWM | 40 | $3.80 | $152 |
| Trigger + Hall sensor | 40 | $2.60 | $104 |
| PTC + wiring | 40 | $1.40 | $56 |
| PA6-GF30 body (low-vol tooling) | 40 | $6.80 | $272 |
| TPE grip | 40 | $1.90 | $76 |
| Assembly + UL cert (amortized) | 40 | $4.60 | $184 |
| **Total** | | | **~$1588** |
| Per-unit landed | | | **~$40** |
