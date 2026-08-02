# Ecosystem

This file gives AI coding agents the repo map and ownership rules.
Keep it updated when the layout changes.

## Repo layout

Single-repo project, solo-owned. No sibling repos yet. Milestone 1
(design-and-contract for `travel-planner`) is built — the structure
below reflects the actual current layout, not a target.

```text
roam-weave/
├── README.md
├── README.zh-CN.md
├── docs/                                  # product decisions, handoff, findings
│   ├── README.md
│   ├── INITIAL_FINDINGS.md
│   ├── HANDOFF.md
│   ├── itinerary-data-model.md            # canonical entity definitions
│   └── superpowers/specs/                 # dated design specs (brainstorming skill output)
├── skills/
│   └── travel-planner/                    # the MVP skill
│       ├── SKILL.md
│       ├── references/                    # destination-research, route-planning,
│       │                                  # one-day-trip, multi-day-trip, quality-check
│       └── templates/                     # destination-brief.md, itinerary.md, itinerary.html
├── schemas/                               # canonical data contracts (JSON Schema)
│   ├── itinerary.schema.json
│   └── traveler-profile.schema.json
└── tests/
    └── fixtures/                          # oxford-one-day/, edinburgh-family-3-day/
```

No `destination-brief.schema.json` yet — Destination Briefs stay
Markdown-only this milestone
(`docs/itinerary-data-model.md` §1). No `docs/validation-rules.md` —
the validation checklist lives at
`skills/travel-planner/references/quality-check.md` instead, as
documentation, not executable code (design spec §7).

## Repos and ownership

Single repo — one owner (weiwei cao). No cross-repo ownership table
needed. Within the repo, treat these as separate concerns so changes
stay minimal and reviewable:

| Area | Path | Owns |
|---|---|---|
| Product decisions | `docs/` | Rationale, scope, MVP boundaries — read before changing behavior |
| Skill logic | `skills/travel-planner/` | Planning workflow, rules, references, templates |
| Data contracts | `schemas/` | Canonical itinerary/destination-brief structure |
| Fixtures | `tests/fixtures/` | Realistic example inputs/outputs for the schema |

## Request / data flow

Two distinct pipelines, per `docs/HANDOFF.md` §3.4 and §3.6:

```text
Destination research
→ normalized destination data (Destination Brief)
→ traveler-specific selection + route draft (Trip Itinerary)
→ human refinement
→ validation
→ Markdown / HTML / map rendering
```

Structured data (schemas/) is the source of truth. Markdown/HTML are
presentation formats generated from it, not edited directly as canon.

## Ownership rules

Single repo, so "ownership" here means "which layer should change":

### Planning logic / route mistakes

1. `skills/travel-planner/references/*` — rule text
2. `skills/travel-planner/SKILL.md` — workflow routing
3. `schemas/itinerary.schema.json` — if the mistake is a missing/underspecified field

### Data model / validation gaps

1. `docs/itinerary-data-model.md` — field semantics
2. `schemas/*.schema.json` — schema definition
3. `skills/travel-planner/references/quality-check.md` — validation checklist (documentation-only, not executable code — design spec §7)

### Product-scope questions ("should this exist at all")

1. `docs/HANDOFF.md` §6.4 (MVP non-goals) — check before adding scope
2. `docs/INITIAL_FINDINGS.md` — original rationale

## AI safety rules

- Do not edit code before root cause and owning file/module are reasonably confirmed.
- Do not make broad refactors unless the ticket explicitly requires it.
- Respect the MVP non-goals in `docs/HANDOFF.md` §6.4 — no booking, browser automation, external APIs, or web framework in this milestone.
- Prefer minimal, reviewable diffs.
- Always check current branch and git status before editing.
- Always summarize changed files and test coverage before PR.
