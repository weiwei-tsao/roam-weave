# Itinerary Data Model

Canonical entity definitions for the `travel-planner` skill. Structured
data is the source of truth (`docs/HANDOFF.md` §3.6) — Markdown and HTML
are rendered from these entities, never edited as canon.

This document defines field semantics in prose/table form.
`schemas/itinerary.schema.json` and `schemas/traveler-profile.schema.json`
are the JSON Schema encodings of what's defined here; if the two ever
disagree, this document wins and the schema should be updated to match.

## 1. Two top-level entities, two different lifetimes

| | Destination Brief | Trip Itinerary |
|---|---|---|
| Answers | What exists at this destination? | What should this traveler do, this trip? |
| Lifetime | Reusable across multiple trips to the same destination | One planning session, one trip |
| Contains | The full candidate set, tiered | A curated, dated, ordered subset + everything that was *not* chosen |
| Schema | `schemas/destination-brief.schema.json` * | `schemas/itinerary.schema.json` |

\* Not in this milestone's deliverable list (`docs/HANDOFF.md` §7 proposes
it for a later pass); until it exists, a Destination Brief is produced as
Markdown only, and its `Candidate` entities (§2 below) are carried into
the Trip Itinerary by copying the fields a `Stop` needs. Revisit once a
second Destination Brief is generated for the same place and reuse
actually matters.

## 2. Destination Brief entities

### DestinationBrief

| Field | Type | Notes |
|---|---|---|
| `destination` | string | Canonical display name |
| `local_name` | string \| null | Local-language name, must resolve in a map search (`docs/HANDOFF.md` §10) |
| `summary` | string | Short orientation paragraph |
| `candidates` | `Candidate[]` | The full tiered set — see below |
| `cost_saving_tips` | string[] | |
| `general_notes` | string[] | Destination-wide warnings/practical notes |
| `generated_at` | date | |

### Candidate

One place or experience the destination offers. Tiering and fields follow
the reference workflow's "必玩/推荐/可玩" structure validated in
`docs/superpowers/specs/2026-08-01-travel-planner-mvp-design.md` §5 item 2.

| Field | Type | Notes |
|---|---|---|
| `id` | string | Stable slug; referenced by `Stop.candidate_id` and `CandidateStatus.candidate_id` |
| `name` | string | Display name |
| `local_name` | string \| null | |
| `tier` | `must_see \| recommended \| optional` | |
| `category` | string | Free text (museum, viewpoint, neighborhood, ...) — no fixed enum, avoid overfitting one destination's taxonomy onto all destinations |
| `area` | string \| null | Neighborhood/geographic cluster, used for day-grouping |
| `description` | string | History, key eras, core experience — a **stable fact**, rarely changes |
| `photography_note` | string \| null | Qualitative (best light, angle) — no numeric "score" (avoids false precision, principle 5 in `docs/INITIAL_FINDINGS.md` §8) |
| `reservation` | `required \| recommended \| not_needed` | |
| `opening_hours` | string \| null | **Dynamic fact** — needs `sources` |
| `last_admission` | string \| null | **Dynamic fact** |
| `estimated_cost` | `{ amount: number, currency: string }` \| null | **Dynamic fact** — currency is mandatory whenever `amount` is set (`docs/HANDOFF.md` §5.4) |
| `sources` | `Source[]` | Required whenever any dynamic-fact field above is set |
| `notes` | string[] | |

## 3. Trip Itinerary entities

### Trip

The root entity for one planning session.

| Field | Type | Notes |
|---|---|---|
| `destination` | string | |
| `dates` | `{ start: date, end: date }` \| null | Null when dates are unknown — see `assumptions` |
| `duration_days` | number \| null | Used instead of `dates` when dates aren't fixed yet |
| `origin` | string \| null | |
| `arrival` / `departure` | `{ mode: string, time: string \| null, notes: string[] }` \| null | |
| `accommodation` | `{ name: string, area: string, confirmed: boolean }` \| null | |
| `travelers` | `{ count: number, notes: string[] }` | Ages, accessibility, children/seniors go in `notes` |
| `pace` | `relaxed \| moderate \| packed` | **User preference**, from Traveler Profile or stated this session |
| `walking_tolerance` | string \| null | **User preference** |
| `budget_range` | string \| null | **User preference** |
| `interests` | string[] | **User preference** |
| `days` | `Day[]` | |
| `constraints` | `Constraint[]` | |
| `assumptions` | `Assumption[]` | |
| `validation_issues` | `ValidationIssue[]` | |
| `candidate_status` | `CandidateStatus[]` | Every `Candidate` from the Destination Brief, included or not — §5 |

`pace`, `walking_tolerance`, `budget_range`, and `interests` are trip-level
copies of Traveler Profile fields (durable, out-of-repo per
`docs/superpowers/specs/2026-08-01-travel-planner-mvp-design.md` §4) —
they're duplicated onto `Trip` because a single trip may override the
durable profile (e.g. traveling with different companions this time), and
the itinerary must be interpretable without the profile file present.

### Day

| Field | Type | Notes |
|---|---|---|
| `date` | date \| null | Null if `Trip.dates` is null |
| `day_number` | number | 1-indexed, always present |
| `area` | string | The one primary area/theme for the day (`docs/INITIAL_FINDINGS.md` §8 principle 1) |
| `design_note` | string | **Model recommendation** — a short paragraph explaining the day's route logic, written before the stop list (mirrors the reference workflow's "路线开头先用一小段正文说明路线设计逻辑") |
| `estimated_walk_km` | number \| null | Approximate, not GPS-precise |
| `intensity` | `light \| moderate \| high` | Qualitative, not a fitness-tracker metric |
| `stops` | `Stop[]` | |
| `transit_legs` | `TransitLeg[]` | |
| `meal_options` | `MealOption[]` | |
| `notes` | string[] | |

### Stop

| Field | Type | Notes |
|---|---|---|
| `candidate_id` | string \| null | Link back to the Destination Brief `Candidate` this stop came from, if any |
| `name` | string | |
| `local_name` | string \| null | |
| `arrival` / `departure` | time \| null | Windows, not to-the-minute schedules (principle 5) |
| `reservation_status` | `required \| recommended \| not_needed` | |
| `reservation_url` | string \| null | Required whenever `reservation_status` is `required` |
| `last_entry` | string \| null | Mandatory when this is the day's last stop and the venue has a last-admission time |
| `source` | `Source` \| null | |
| `notes` | string[] | |

### TransitLeg

| Field | Type | Notes |
|---|---|---|
| `from` / `to` | string | |
| `mode` | string | walk / train / bus / taxi / ... |
| `estimated_duration_minutes` | number \| null | |
| `notes` | string[] | |

### MealOption

| Field | Type | Notes |
|---|---|---|
| `name` | string \| null | |
| `area` | string | |
| `note` | string | Why it's a candidate — restaurants are **candidates, not route anchors** unless food is a stated interest (`docs/INITIAL_FINDINGS.md` §8 principle 6) |

### Source

Attached to any dynamic fact (opening hours, prices, reservation rules).

| Field | Type | Notes |
|---|---|---|
| `url` | string | |
| `checked_at` | date | |
| `confidence` | `verified \| uncertain` | `uncertain` means the Agent could not fully confirm — must be surfaced to the user, not silently treated as fact |

### Constraint

| Field | Type | Notes |
|---|---|---|
| `description` | string | |
| `kind` | `hard \| soft` | Hard = confirmed dates/hotel/mobility needs; soft = a preference that can be traded off (`docs/INITIAL_FINDINGS.md` §8 principle 3) |
| `source` | `user_stated \| inferred` | |

### Assumption

Represents information the user didn't provide, per `docs/HANDOFF.md` §6.2
("Missing information should be represented as assumptions or open
questions, not silently invented").

| Field | Type | Notes |
|---|---|---|
| `description` | string | |
| `reason` | string | What information was missing that forced this assumption |
| `confirmed` | boolean | Flips to `true` once the user confirms or corrects it in a later revision |

### ValidationIssue

One finding from applying `skills/travel-planner/references/quality-check.md`.

| Field | Type | Notes |
|---|---|---|
| `rule` | string | Which checklist rule triggered this |
| `severity` | `blocker \| warning \| note` | |
| `description` | string | |
| `location` | string | e.g. `"day 2 / stop 3"` |
| `resolved` | boolean | |

### CandidateStatus

One row per `Candidate` in the source Destination Brief — this is what
renders as the trip's trailing ✅/❌ list.

| Field | Type | Notes |
|---|---|---|
| `candidate_id` | string | |
| `included` | boolean | |
| `reason` | string | **Required**, even when `included` is true — explains why it made the cut or was deferred/removed (`docs/HANDOFF.md` §5.4) |

## 4. Facts, assumptions, preferences, recommendations

Four kinds of content appear throughout the model above. There's no
generic wrapper type for this — the distinction is structural and
positional, not a tagged field on every value:

- **Stable facts** (`Candidate.description`, `Candidate.category`) — verified
  once, changes rarely, no `Source` required.
- **Dynamic facts** (`opening_hours`, `estimated_cost`, `reservation_status`,
  anything time/price/availability-sensitive) — always paired with a
  `Source` entity; never asserted without one.
- **User preferences** (`Trip.pace`, `Trip.interests`, `Constraint` where
  `source = user_stated`) — come from the user, verbatim or lightly
  normalized, never invented.
- **Model recommendations** (`Day.design_note`, `MealOption.note`,
  `Day.intensity`) — the Agent's judgment, always phrased as such, never
  presented with the same confidence as a verified fact.

## 5. Exclusion tracking

Every `Candidate` in the Destination Brief gets exactly one
`CandidateStatus` entry in the `Trip`. This is what lets a Markdown/HTML
render end with the ✅/❌ list validated against the reference workflow
(design spec §5 item 2) — nothing from the brief silently disappears;
every omission has a `reason`.

## 6. Relationship to Traveler Profile

`Trip` does not embed a full Traveler Profile. It copies the handful of
fields (`pace`, `walking_tolerance`, `budget_range`, `interests`) that
directly shape route decisions. The full profile
(`schemas/traveler-profile.schema.json`) is a separate, longer-lived,
out-of-repo artifact — see
`docs/superpowers/specs/2026-08-01-travel-planner-mvp-design.md` §4 for
why it isn't merged into this file.

## 7. Explicitly not modeled (Milestone 1)

Per `docs/HANDOFF.md` §6.4 and design spec §8: no booking/reservation
transaction state beyond `reservation_status`, no price-comparison
entities, no map-geometry fields (lat/lng, polylines), no museum-specific
sub-entities (exhibit halls, visit order), no photography scoring beyond
a free-text note. Adding any of these should start with a non-goals
review, not a schema edit.
