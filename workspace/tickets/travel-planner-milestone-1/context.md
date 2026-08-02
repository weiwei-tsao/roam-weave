# travel-planner-milestone-1 Context

## Goal

Another agent (or Codex) can read the repository and consistently produce
a structured itinerary plus Markdown/HTML output, with no hidden context
from prior conversations — i.e. `docs/HANDOFF.md` §8's Definition of Done,
concretized by `docs/superpowers/specs/2026-08-01-travel-planner-mvp-design.md`.

## User-visible problem / business need

RoamWeave has product-discovery docs (`docs/INITIAL_FINDINGS.md`,
`docs/HANDOFF.md`) but zero implementation: no `skills/`, no `schemas/`,
no data model doc. This is a greenfield build ticket, not a bug fix —
"investigation" here means confirming the design is complete enough to
build from, not root-causing a defect.

## Expected behavior

`skills/travel-planner/`, `schemas/`, and `docs/itinerary-data-model.md`
exist and match the design spec; ≥2 fixtures demonstrate the contract
end-to-end (one one-day trip, one multi-day/constrained trip).

## Current behavior

Repo has only `docs/` (product-discovery + this ticket's design spec) and
the unrelated `workspace/` ticket-tooling scaffold. No `skills/`,
`schemas/`, or `tests/` directories exist yet.

## Scope

### In scope

- `skills/travel-planner/SKILL.md` + `references/*.md` (destination-research,
  route-planning, one-day-trip, multi-day-trip, quality-check)
- `docs/itinerary-data-model.md`
- `schemas/itinerary.schema.json`, `schemas/traveler-profile.schema.json`
- Markdown templates (Destination Brief, Itinerary) + one single-file HTML template
- ≥2 fixtures under `tests/fixtures/`

### Out of scope

- Everything in `docs/HANDOFF.md` §6.4 and the design spec §8: booking/price
  comparison, platform account interaction, PDF reports, generated maps,
  museum/photography specialty modules, deploy tooling.
- Executable validator code (`quality-check.md` stays documentation-only —
  design spec §7).
- Slash-command/CLI parsing for skill invocation (natural language only).

## Repos / modules likely involved

| Area | Repo / module | Why it may be involved |
|---|---|---|
| Skill logic | `skills/travel-planner/` | Core deliverable — planning workflow, rules, templates |
| Data contracts | `schemas/` | Canonical itinerary/destination-brief structure |
| Product decisions | `docs/` | `itinerary-data-model.md` is a new deliverable; `HANDOFF.md`/`INITIAL_FINDINGS.md` are read-only references |
| Fixtures | `tests/fixtures/` | Realistic example inputs/outputs for the schema |

## Links

- Ticket: `workspace/tickets/travel-planner-milestone-1/`
- PR: (not yet opened)
- Design/spec: `docs/superpowers/specs/2026-08-01-travel-planner-mvp-design.md`

## Important constraints

- Do not edit code until ownership is confirmed.
- Keep the diff minimal.
- Avoid unrelated formatting.
- Respect MVP non-goals (`docs/HANDOFF.md` §6.4) — re-check before merging (`workspace/conventions.md` :: Stages / acceptance).

## Short title

Travel-planner MVP Milestone 1: design-and-contract build-out
