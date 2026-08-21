---
name: frostpunk2-utopia-builder
description: 'Generate a Frostpunk 2 Utopia Builder setup — Tale(s), Communities, Faction, Map, Difficulty, Mods, Ambition — from a request like "I want to try Horizon", "give me an easy start", or "I just finished #8, give me something harder". Reuses a curated scenario when one already fits; otherwise synthesizes a new combination from the Community-to-Faction gating and Zeitgeist rules, mirroring the reasoning behind the existing curated set.'
disable-model-invocation: true
allowed-tools: Read
---

# Frostpunk 2 — Utopia Builder Config Generator

Produce exactly **one** config, as a single filled record — never a table, never multiple options to choose from:

```
Tale(s):      ...
Communities:  ... · ... · ...
Faction:      ...
Map:          ...
Difficulty:   ...
Mods:         ... (or —)
Ambition:     ...
```

Follow it with 1-3 short bullets of rationale in the source material's voice — why this map, why this Faction answers the Tale's core demand, one early move. If a curated row was used, cite its `#` and ★ status instead of re-deriving rationale. One config, one rationale — not a spread of alternatives.

**Hard rule, never violate:** the Faction must be one of the two unlocked by the three chosen Communities (gating table below). Every other choice is a judgment call; this one is a game rule.

## Step 1 — Classify the request

- **Direct pick** — names a map, Tale, or Ambition with no other constraint → Step 2, then Step 3 if nothing matches.
- **Difficulty request** — "easier", "harder", "more of a challenge", "I just finished X, give me another" → Step 4.
- **Open request** — "give me a config", "surprise me", "easy start", no specifics → Step 3.
- **Custom constraints** — pins 2+ of {map, Tale, Faction, Community, Ambition} in a combination the curated set doesn't already cover → Step 5.

## Step 2 — Check the curated scenarios first

Read `reference/scenarios.csv` (32 rows: 24 one-Tale + 8 two-Tale) for a row matching the request. Prefer reuse over synthesis whenever a row already fits — that's the point of the curated set.

- **New map** → its first-listed row (lowest `#` for that map) — the guide marks this the approachable entry for that map.
- **New Tale** → its ★ row — reproduces the game's own recommended Communities/Map/Ambition (cross-check `reference/defaults.md`).
- **"Same setup but easier"** on an already-identified row → keep Map/Tale/Faction, drop the Suggested Ambition to *No Ambition*, and/or add Serenity Mode. Difficulty preset and Serenity/Survivor are explicitly global toggles in the source, not row-specific.
- If several rows match, prefer the lower `#` (rows are ordered easiest-to-hardest per map) unless the request's wording points at the harder one.

Found a fitting row → present it (cite `#`/★), skip Steps 4-5. Nothing fits → continue.

## Step 3 — Open-request defaults

- **No constraints at all** (no map, Tale, Faction, Community or Ambition named) → don't default silently. Ask the user for a Map or Tale preference (AskUserQuestion — offer a couple of Map and Tale options, e.g. Windswept Peaks/a new map, or a specific Tale) before picking a row. Once they answer, resolve it via Step 2/3's other bullets.
- "easy" / "beginner" / "learning" with no map/Tale named → still ask (as above), but frame the options toward easy picks: Windswept Peaks, row `#1`/`#2`, Officer difficulty, Serenity Mode optional.
- "new Tale X" → its ★ row.
- "new map X" → its first-listed row.
- "new Ambition X" → `#2` Repopulate the Frostland · `#1` Develop a Metropolis · `#3` Build a Prosperous Future (per the guide's own "Where to start" pointers), else any 1-Tale row already carrying that Suggested Ambition.

## Step 4 — Escalate or de-escalate from a finished setup

Identify the row (or nearest one) the user just played. Move along the ladder — pick 1-3 rungs, more rungs for a bigger jump:

**Harder:**
1. If the map appears in the 2-Tale table, take that pairing (stacks a second crisis on the same map).
2. Otherwise move the Map up a difficulty tier (below), keeping the Tale if a curated row exists there.
3. Bump Difficulty preset one tier: Officer → Steward → Captain.
4. Drop Serenity Mode if present; add Volcanic Night (fits Twilight Waters thematically, or layer it as pure extra challenge per rows `#7`/`#15`/`#24`) and/or Survivor Mode for permadeath stakes.
5. Swap Ambition upward (No Ambition/Repopulate → Metropolis/Prosperous Future), or let the 2-Tale stack itself carry the difficulty increase.

Skip the near-impossible 2-Tale pairings (`Depleted Cores + Plague`, `Depleted Cores + Doomsayers` — flagged in `reference/scenarios.md` as not realistically completable) unless the user explicitly asks for a near-unwinnable stack.

**Easier:** reverse each rung — drop to 1 Tale, move the Map down a tier or take its first-listed row, Difficulty preset down one tier (floor Citizen), add Serenity / drop Volcanic Night & Survivor, swap Ambition down, and prefer Overseers (the strongest all-round Faction) if the Tale allows it.

## Step 5 — Synthesize a new combination

No curated row fits and the request pins specifics. Work in this order:

1. **Tale(s)** — from the Tale table below, matching the requested theme/difficulty. One Tale by default; two only on an explicit "hard/stack/expert" ask.
2. **Communities (exactly 3)** — anchor on that Tale's recommended trio in `reference/defaults.md`, then swap at most one Community for whatever the user specifically asked for, provided the trio still unlocks a Faction that answers the Tale's key demand.
3. **Faction** — from the ≤2 Factions the trio unlocks (gating table), pick by matching its perk/action (Faction table below) to the Tale's key demand or the user's stated difficulty intent. Prefer the specialist over the softener when the user wants a real challenge; prefer Overseers/Bohemians-style softeners when they want it easy.
4. **Map** — match requested size/character/difficulty tier; cross-check `reference/wiki.md` and `reference/hints.md` for hazards that interact with the chosen Tale (e.g. Plague + The Pit's disease-heavy pit only works because Proteans answer it directly — row `#9` mirrors this; don't pair a Tale with a map hazard nothing in the setup counters).
5. **Ambition** — the Tale's recommended Ambition from `reference/defaults.md` unless overridden; harder = Metropolis/Prosperous Future, easier = No Ambition/Repopulate.
6. **Difficulty + Mods** — Officer/no mods by default, then apply the user's stated difficulty intent.
7. **Sanity check** — compare the 3 Communities' natural Zeitgeist leanings (wiki axis table) against the chosen Faction's 3 axis positions. A mixed trio is normal (the game's own recommendations routinely span both ends of an axis); only flag it as a caveat — never block on it — if all three Communities oppose the Faction on every axis, since that slows Utopia Tree progress.

## Core rules (apply on every synthesis)

### Community → Faction gating

| Community in the trio | Unlocks |
|---|---|
| Machinists | Overseers · Bohemians |
| Foragers *or* Merchants | Venturers · Menders |
| Labourers *or* Thinkers | Technocrats · Icebloods |
| Lords | Proteans · Legionnaires |

### Factions — axis, kit, best-fit

| Faction | Technology·Economy·Society | Perk | Action | Best-fit |
|---|---|---|---|---|
| Overseers | Progress·Merit·Tradition | Crime reduced citywide | Overdrive Output (temp boost, all resources) | Metropolis, Prosperous Future; strongest all-round/easy pick |
| Bohemians | Adaptation·Equality·Reason | Periodic relations boost | Mindshaping (raise Trust) | Tension/Trust valve (Beacon influx, Doomsayers); weak on raw output |
| Venturers | Progress·Merit·Reason | + Heatstamp income | Finance mercenaries (extra Guard Squads) | Depleted Cores rebuild, Repopulate colony pushes |
| Menders | Adaptation·Equality·Tradition | Faster expeditions | Rescue operations (population boost) | Plague, Beacon refugee waves |
| Technocrats | Progress·Equality·Reason | − Materials demand | Optimize Research (research speed) | Scarce maps, research races (vaccine, insulation) |
| Icebloods | Adaptation·Merit·Tradition | + Food production | Volunteer explorations (Food boost) | Apocalyptic Whiteout, food-poor maps (Dreadnought) |
| Proteans | Adaptation·Merit·Reason | − Disease citywide | Patient Care (sick keep working) | Plague/Beacon health, whiteout illness spikes |
| Legionnaires | Progress·Equality·Tradition | + Guard Squads citywide | Raise Prefabs | Doomsayers, unrest, early expansion |

Rival = the Faction with the fully opposite axis triple (e.g. Overseers ↔ Bohemians, Icebloods ↔ Technocrats, Legionnaires ↔ Proteans, Menders ↔ Venturers) — that rival forms later regardless of the setup, so a synthesis leaning hard toward one axis end should expect that rival as the opposition.

### Tale — crisis & key demand

| Tale | Crisis | Key demand |
|---|---|---|
| Beacon of Hope | Build the Beacon, absorb 5 refugee waves, survive a whiteout finale | Housing surplus, food/heat stockpile, crime control |
| Apocalyptic Whiteout | Survive a ~120-week severe whiteout, under half population lost | Huge fuel/heat + labour to plug heat leaks |
| Depleted Cores | Starting Steam Core removed; rebuild the IEC Core Factory; whiteout ~80wk later | Expeditions, logistics, geothermal/oil |
| Doomsayers (DLC) | Hostile faction hijack attempt after the first whiteout | Tension control, security, politics |
| Plague (DLC) | Disease spreads, contaminates districts; quarantine + vaccine race | Health infrastructure + research |

### Map difficulty tiers

Windswept Peaks (M, easiest) → Crater (L, easy) → Sleeping Mountain (L, easy-moderate) → The Pit (L) / Forsaken Valley (M) (moderate) → Fractured Gorge (S) / Twilight Waters (S) / Jagged Bay (S) (moderate-hard) → Hanging Rock (S) / Dreadnought (S) (hard) → Broken Shore (L, hard but well-liked) → Horizon (XL, hardest).

### Ambition

| Ambition | Goal |
|---|---|
| Repopulate the Frostland | Expand via colonies (Logistics Districts on Old Waystations + expeditions) |
| Develop a Metropolis | Grow population large while keeping every problem below Overwhelming |
| Build a Prosperous Future | Stockpile ~100k each of Fuel/Food/Materials/Goods |
| No Ambition | Sandbox, no win condition |

### Difficulty presets & Mods

Citizen (learner) < Officer (baseline, fair) < Steward (scarce) < Captain (punishing). Each preset can be split per axis (Economy/Weather/Frostland/Society). Mods are independent toggles, stackable on anything: **Serenity Mode** softens (shorter/milder cold, starter production, richer Frostland, 5 Steam Cores); **Volcanic Night** adds tremor/lava-shock events, punishes sprawl; **Survivor Mode** adds no-reload permadeath.

## Reference files

- `reference/scenarios.csv` / `reference/scenarios.md` — the 32 curated rows (machine-parseable / with per-row hints and the "Where to start" navigation notes). Check first, per Step 2.
- `reference/defaults.md` — the game's own recommended Communities/Map/Ambition per Tale. Anchor for Step 5.2 and Step 5.5.
- `reference/wiki.md` — full mechanics reference: Tale, Map, Community/Faction, Zeitgeist axis, Mod, Ambition and Difficulty tables.
- `reference/hints.md` — long-form playthrough hints per Tale, Map, Faction and Ambition — the "why" behind every curated row; mine this for rationale bullets and for Step 5.4's hazard cross-check.
