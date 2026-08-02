---
name: travel-planner
description: Use when the user wants to research a travel destination, build a trip itinerary, or revise an existing one — turns preferences, constraints, and researched facts into an explainable, editable, validated itinerary. Triggers on requests like "research/enumerate <destination>", "what's worth seeing in <destination>", "plan a one-day/N-day trip to <destination>", "generate an itinerary for <destination>", or natural-language edits to an itinerary already in the conversation ("move the museum to the morning", "drop that stop", "day 2 is too packed", "I don't want an early start").
---

# Travel Planner

Turns a destination and a traveler's constraints into a researched,
realistic, editable trip itinerary. Planning only — no booking, search, or
price comparison (`docs/HANDOFF.md` §6.4, design spec §8).

Read `docs/itinerary-data-model.md` before generating or editing any
itinerary — it defines every entity and field referenced below.

## Two flows

### 1. Discover — destination research

Trigger: the user asks what a destination offers, without yet asking for a
day-by-day plan ("research Oxford", "what's worth seeing in Kyoto",
"enumerate Lisbon").

Produces a **Destination Brief** (`docs/itinerary-data-model.md` §2):
`Candidate` places tiered as `must_see` / `recommended` / `optional`, each
with description, reservation requirement, opening hours + source, and
cost. Not scoped to any particular trip length — reusable if the user asks
about the same destination again later in the conversation.

Follow `references/destination-research.md` for how to research and tier
candidates.

### 2. Plan + Refine — itinerary generation and revision

Trigger: the user asks for a day-by-day plan ("plan a one-day trip to
Oxford", "generate a 3-day Edinburgh itinerary"), **or** gives a
natural-language edit to an itinerary already produced in this
conversation.

Produces or updates a **Trip Itinerary**
(`docs/itinerary-data-model.md` §3, `schemas/itinerary.schema.json`).

- First generation: follow `references/route-planning.md`, then
  `references/one-day-trip.md` or `references/multi-day-trip.md`
  depending on trip length.
- Edits to an existing itinerary: do not regenerate the whole document.
  Follow the revision model in `references/route-planning.md` — locate
  the affected `Day`/`Stop`/`TransitLeg` entries, recalculate only what
  the edit affects, and report what changed and why.

After any generation or edit, apply `references/quality-check.md` before
presenting the result.

## Inputs

Accept whatever the user provides, even if partial: destination, dates or
duration, origin/arrival/departure, known accommodation, traveler count
and needs, must-see/excluded places, interests, pace, walking tolerance,
budget, food and transport preferences. Do not ask for everything up
front — missing information becomes an `Assumption` (see below), not a
blocking question, unless the trip cannot proceed without it (e.g. no
destination given at all).

## Assumptions and missing information

Per `docs/HANDOFF.md` §6.2, never silently invent missing facts. When
information is missing:

- If it's needed to produce a useful first draft (e.g. destination), ask.
- Otherwise, record an `Assumption` entity (`docs/itinerary-data-model.md`
  §3) with a `reason`, proceed with a reasonable default, and surface it
  in the output so the user can correct it.

## Output

Markdown is the default and baseline output — a Destination Brief or Trip
Itinerary rendered from `templates/destination-brief.md` or
`templates/itinerary.md`. Every Trip Itinerary ends with the ✅/❌
candidate list (`docs/itinerary-data-model.md` §5) so nothing from the
Destination Brief silently disappears.

Render `templates/itinerary.html` (single-file HTML, same data, no maps or
interactivity) only when the user asks for an HTML/shareable version.

## Explicitly out of scope

No booking or price comparison, no platform account interaction, no
generated maps, no PDF reports, no museum- or photography-specific
modules, no slash-command/CLI syntax for invoking this skill — natural
language only. Full list: `docs/HANDOFF.md` §6.4.

## Reference files

| File | Covers |
|---|---|
| `references/destination-research.md` | How to research and tier Destination Brief candidates |
| `references/route-planning.md` | Route design principles + the natural-language revision/recalculation model |
| `references/one-day-trip.md` | One-day-specific planning rules |
| `references/multi-day-trip.md` | Multi-day-specific planning rules (day-to-day continuity, accommodation) |
| `references/quality-check.md` | Post-generation/post-edit validation checklist |
