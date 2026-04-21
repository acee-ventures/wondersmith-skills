---
name: wondersmith-bike-accessory
description: Use the `wondersmith` CLI to design a bike-mounted electronics device (front/rear lights, GPS beacons, cadence sensors, handlebar controllers) with IPX7 weather-sealing, USB-C rechargeable LiPo, conformal-coat PCB, vibration-tolerant mounts, targeting $30-130 retail. Trigger when the user asks for a device that mounts on a bicycle. Do NOT trigger for e-bike motor controllers, bike computers with full displays, or indoor cycling accessories.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: bike-accessory
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  budgetBand:
    bomMin: 14
    bomMax: 32
    retailTarget: 90
  homepage: https://wondersmith.app
---

# wondersmith — bike accessory

## When to trigger this skill

The user's prompt should combine most of:

- **Mounting**: handlebar / stem / seat post / frame / wheel hub / helmet
- **Environment**: outdoor, rain, mud, salt, road vibration
- **Power**: rechargeable LiPo via USB-C (no user-accessible battery compartment)
- **Retail target**: $30-$130

Example prompts that match:

- "a rear bike light with brake-detect auto-brightening"
- "a BLE cadence sensor that clips to the crank arm"
- "a GPS beacon that pings my phone if the bike leaves a geofence"
- "a handlebar controller for shifting between ebike assist modes"

## Do NOT trigger when

- E-bike motor / battery pack / controller → regulatory + power-electronics complexity beyond wondersmith
- Full-featured bike computer (GPS + map + heart-rate) → use `wondersmith-wearable-fitness` patterns + explicitly request larger budget
- Indoor trainer / smart cycling desk → use `wondersmith-desk-electronics`
- Full helmet → helmets have EN 1078 structural cert that's not in scope

## How to invoke wondersmith

### Quick preview (standard, ~3 min)

```bash
wondersmith design "<user prompt>" --depth standard --target-retail 90
```

### Full design with CAD + IPX7 validation (deliberate, ~15 min, detach)

```bash
wondersmith design "<user prompt>" \
  --depth deliberate \
  --target-retail 90 \
  --detach
wondersmith runs watch <runId>
```

Deliberate is recommended — ingress sealing is easy to draw and hard to build without the critic pass.

## What wondersmith produces for this archetype

- BOM: ~13 components, raw $14-$32, quote-ready at 1.5× ($21-$48)
- Artifacts (deliberate/deep):
  - BOM.csv with conformal-coat spec + weather-sealed USB-C flap MPN
  - `concept.step`, `concept.stl`, `concept.glb` — body + mount + flap
  - `concept.cadquery.py` — parametric body with gasket groove + mounting
  - `concept.kicad_sch` — if BLE or sensor-based
  - `ORDER_INFO.json` — LeadArt plan, ~7 business days
- Suggested depth for this archetype: **deliberate** (IPX7 + vibration specs matter)

## Prompt-writing tips for this archetype

Encourage the user to specify:

- **Mounting point** (handlebar diameter — 22.2 / 25.4 / 31.8 mm are common; seat post clamp style)
- **Target battery life** ("a week of commute", "a full day audax")
- **Light output in lumens** if it's a light ("StVZO-compliant ~50 lux" for DE market, "200 lm front" for US)
- **Visibility priority** (daytime ninja / night commuter / race-aero)

If unmentioned, default to BLE (adds $2 BOM, huge utility) and USB-C charging behind a silicone flap (never metal threaded cap — threads strip).

## Reading the output

- **BOM.csv**: verify PCB line says "conformal coated" (acrylic or urethane). Non-negotiable for outdoor. Flag if missing.
- **3D viewer**: gasket groove should be visible on the mating faces. USB-C should be behind a silicone flap, not a metal plug.
- **Final-report**: thermal fuse on LiPo is required for this archetype (road vibration = small but real physical shock risk). Flag if absent.

## Compliance heads-up

- **IPX7** — 30 min submersion at 1 m. Default for this archetype. The critic sizes the gasket for you.
- **IEC 60068-2-6 class 5** — vibration tolerance (10-500 Hz, 2g for 1 hr/axis). Not a retail cert, but LeadArt's QA samples test to this.
- **StVZO** — German e-bike / bike light cert. Required for DE market headlights. Specific beam shape + reflector geometry. Flag early if user is in DE/AT/CH.
- **FCC Part 15 Class B** — any BLE device. Use pre-certified radio module.
- **RoHS 3 / REACH SVHC** — EU market.
- **UN 38.3** — LiPo shipping.

If the user is cycling-serious (rando, gravel racing), the community cares about thousand-km reliability. Tell wondersmith to default to AEC-Q100 automotive-grade components where available — small BOM premium, big reliability win.
