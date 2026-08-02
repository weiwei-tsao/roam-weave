# Ecosystem

This file gives AI coding agents the repo map and ownership rules.
Keep it updated when the layout changes.

## Repo layout

Single-repo project, solo-owned. No sibling repos yet — the structure
below is the target module layout described in `docs/HANDOFF.md`
(current repo only has `docs/` populated; the rest is planned).

```text
roam-weave/
├── README.md
├── README.zh-CN.md
├── docs/                        # product decisions, handoff, findings
│   ├── README.md
│   ├── INITIAL_FINDINGS.md
│   └── HANDOFF.md
├── skills/
│   └── travel-planner/          # the MVP skill: SKILL.md + references
│       ├── SKILL.md
│       ├── references/
│       ├── templates/
│       └── examples/
├── schemas/                     # canonical data contracts (JSON Schema)
└── tests/
    └── fixtures/
```

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
3. Validation checklist (in skill references or `docs/validation-rules.md`)

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
