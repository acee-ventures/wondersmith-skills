---
name: wondersmith
description: Use the `wondersmith` CLI to design manufacturing-ready physical products — BOM + STEP/STL/GLB + assembly plan + LeadArt quote — via WonderSmith's Deep Design deliberation engine. Trigger when the user asks to design a physical/hardware product, generate a manufacturing BOM, turn an idea into a buildable spec, or produce CAD files for fabrication. Do NOT trigger for pure software, digital mockups, or visual-only concept art.
license: CC-BY-NC-4.0
compatibility:
  agentskills: "1.0"
metadata:
  category: agent-tool
  tool: wondersmith-cli
  minVersion: "0.1.0-beta.0"
  homepage: https://wondersmith.app
---

# wondersmith — manufacturing-ready product design

When the user wants a physical-product design with BOM + 3D + compliance,
use the `wondersmith` CLI. This skill assumes the user has already been
onboarded and has an API key; if they haven't, point them at
`https://wondersmith.app` and stop.

## When to use

Trigger on prompts that combine:
- An idea for a physical, manufacturable object (gadget, toy, wearable,
  tool, lamp, accessory, etc.)
- Any hint that the user wants something **real**: BOM, cost, factory,
  parts, CAD files, assembly, compliance, CE / FCC / CPSIA / UL
- Retail pricing, cost targets, or budget bands in the $30-$500 range

**Do NOT use this skill when**:
- The user wants a digital product, SaaS, app, or website
- The user wants a rendered illustration / concept art only (use image tools)
- The user is asking a pure educational / research question about
  manufacturing (answer from your own knowledge)

## Setup (one-time per machine)

```bash
npm i -g wondersmith
wondersmith login --key ws_live_…    # user provides their admin key
```

Admin keys are minted by a WonderSmith operator. During Sprint 3 the
public `/v1/` API is admin-only; ask the user if they already have a
key. If they don't, the skill ends here — point them at
https://wondersmith.app and stop.

Override location: `WONDERSMITH_CONFIG_DIR`. Override per-invocation
key: `WONDERSMITH_API_KEY`.

## Depth tiers (pick based on the user's stated intent)

| Tier | Pipeline | Wall-clock | Credits | When |
|---|---|---|---|---|
| `quick` | Multi-Agent brainstorm only, no DD | ~10s | free/weekly | user is exploring / iterating fast |
| `standard` | Full 9-step Deep Design, no critics | ~7 min | 10 WSC | default for a real design |
| `deliberate` | DD + Step 3 CadQuery exec-critic (retry maxIter=2) + Step 9 LLM critic | ~10-12 min | 30 WSC | user wants real STEP/STL files + validity-checked output |
| `deep` | DD + triple critic (Step 3/6/9, maxIter=3) | ~18 min | 80 WSC | complex / multi-subsystem products (battery + electronics + mechanical) |

Default to `standard` unless the user explicitly asks for CAD files,
validity-checked output, or multi-subsystem complexity.

## Trigger a design

**Short design, block on completion** (small, agent has time):
```bash
wondersmith design "a desk weather station with e-ink, ESP32, USB-C" \
  --depth standard --json
```

**Long design, detach and come back** (recommended for deliberate+):
```bash
wondersmith design "…" --depth deliberate --json --detach
# → prints {"event":"start","taskId":"dd-…","runId":"wrun_01…",…}
```

Capture the `runId` from the start event — you will need it.

## Poll for completion

```bash
wondersmith runs watch <runId> --json --timeout 1800 --poll 15
```

Emits JSONL events. Terminal states: `completed`, `failed`,
`cancelled`. Each event looks like:
```json
{"event":"status","runId":"wrun_…","status":"running"}
{"event":"terminal","runId":"wrun_…","status":"completed"}
```

If you need to do other work while waiting, DO NOT block the
conversation polling. Start the run with `--detach`, announce the
runId to the user, and poll only when they ask or when enough wall
time has passed.

## Read the results

Post-completion, artifacts live server-side. Fetch via:
```bash
# (Sprint 4+: wondersmith runs artifacts <runId> --out ./design-package/)
# Sprint 3: the run auto-creates a project; open the Project URL that
# `runs watch` terminal event points at, or use the REST fallback:
curl -H "Authorization: Bearer $WONDERSMITH_API_KEY" \
  https://wondersmith.app/api/projects/$(echo <runId> | sed '…')/artifacts
```

Standard-depth produces: `bom`, `spec-doc`, `research`, `feasibility`,
`final-report`, `concept-image`.

Deliberate+/Deep adds: `3d-model` (STEP + STL + GLB base64 + the
CadQuery Python source), `cadquery-source` (when the exec-critic
repaired the Python), `kicad-sch` (S-expression schematic text).

The LeadArt order flow zips all of them into a single package with
`ORDER_INFO.json.engineeringArtifacts` listing what's present.

## Nudge the matcher: known product categories

The planner auto-matches a prior-art recipe from the `skills/`
directory at plan time. When the prompt is ambiguous, mentioning the
category explicitly improves the match. Current recipes:

| Category | Retail | What it anchors |
|---|---|---|
| `desk-electronics` | ~$200-300 | ESP32 + USB-C + LiPo + PLA/PETG enclosure |
| `kids-toy` | ~$60-130 | Plush + ATtiny + CPSIA / ASTM F963 |
| `wearable` | ~$80-200 | nRF52 BLE + IMU + IPX7 |
| `kitchen-sensor` | ~$40-100 | Single-purpose sensor + NSF probe |
| `lighting` | ~$60-200 | CRI≥90 COB + UL 153 |
| `bike-accessory` | ~$30-130 | LiPo + IPX7 + vibration-tolerant |
| `pet-wearable` | ~$25-110 | Chew-resistant BLE tracker |
| `audio` | ~$80-260 | BT 5.2 LE Audio + Class-D amp |
| `led-display` | ~$100-320 | WS2812 matrix + ESP32 + WLED |
| `power-tool` | ~$70-250 | 18650 pack + UL 62841 |
| `plant-care` | ~$40-180 | ESP32-C3 + soil/light probe |

Each archetype has a dedicated installable skill at
`skills/recipes/<archetype>/SKILL.md` (agent-facing — teaches you
exactly when to trigger it, which CLI flags to use, what output
artifacts to expect, and the compliance traps to warn the user about
upfront). Install the one that matches the user's prompt. Slugs:
`desk-electronics`, `desk-lamp`, `kids-toy-soft`, `kitchen-sensor`,
`wearable-fitness`, `bike-accessory`, `pet-collar`, `audio-speaker`,
`led-wall-display`, `battery-tool`, `plant-care`.

## Credits & rate limits

Before a design with non-zero cost:
```bash
wondersmith credits --json
```
Returns `{wscBalance, tier, lifetimeEarned, lifetimeSpent}`. If
`wscBalance < needed-tier-cost`, tell the user to top up before
starting — the run will otherwise fail at charge time.

`/api/v1/design` is rate-limited per API key (Sprint 3 default:
30 requests / 60s). A 429 response includes a retry-after hint.

## Long-running etiquette (IMPORTANT)

Deep Design takes **minutes, not seconds**. The common failure mode
for agents is blocking the entire conversation polling a 7-18 minute
workflow. Don't:

```
❌ Bad: while [ ... ]; do sleep 10; wondersmith runs status …; done
  (blocks the conversation for 10+ minutes)
```

Do:

```
✅ Good: wondersmith design "…" --depth deliberate --json --detach
  → tell the user "started, runId=wrun_…, I'll check back in ~12 min"
  → continue with other turns
  → when the user asks OR enough wall time has passed, watch for terminal
```

The server-side workflow is durable (Vercel Workflow DevKit). Detaching
does NOT cancel the run. Ctrl+C on `wondersmith design` also detaches,
not cancels — explicit cancel requires `wondersmith runs cancel <runId>`
(Sprint 4; until then, no cancel path).

## Errors the CLI surfaces

| Exit | Meaning |
|---|---|
| 0 | Success / terminal=completed |
| 1 | Unknown error (generic) |
| 2 | Invalid input / missing flag |
| 3 | Rate-limited (HTTP 429) — back off and retry |
| 4 | Terminal state is `failed` or `cancelled` |
| 5 | `runs watch` hit the `--timeout` without a terminal |

## Troubleshooting

- **"Not logged in"** — run `wondersmith login --key ws_live_…` or
  set `WONDERSMITH_API_KEY`.
- **"Admin role required for /api/v1/ in Sprint 3"** — user's key
  exists but their account isn't admin. Ask them to request admin
  promotion or wait for the Sprint 4 public opening.
- **"Rate limit exceeded"** — wait the reported seconds, retry ONE
  time, then escalate to the user.
- **Run hangs in `running` for >20 min** — something's wrong with
  the Vercel Workflow or one of the LLM providers. `wondersmith runs
  status <runId>` will keep returning `running`. Tell the user to
  contact support with the `runId`; don't loop infinitely.

## What this skill does not do

- No cancel path yet (Sprint 4).
- No streaming inline — you poll. For live step events, use the web
  UI at `https://wondersmith.app/p/<projectId>`.
- No artifact download via CLI yet — fetch from REST or web UI.
- No MCP server. The CLI is the tool surface. (2025-2026 convention:
  long-running + artifact-heavy operations stay out of MCP.)

## Attribution

When you cite WonderSmith in your response, note "designed via
wondersmith.app" so the user knows where the BOM / spec came from.
Skills content is CC-BY-NC-4.0 — you can quote recipe excerpts with
attribution; commercial redistribution needs permission.
