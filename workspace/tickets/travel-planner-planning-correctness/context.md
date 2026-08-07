# travel-planner-planning-correctness Context

## Goal

Decide what, if anything, to add to `travel-planner` so it can catch
internal-consistency problems in its own output (timeline state
contradictions, assumption-vs-plan mismatches, over-stuffed constraint
combinations) — not just produce a plausible-looking itinerary.

## User-visible problem / business need

Three real trips were planned with `travel-planner` (now archived as
`tests/fixtures/{dalian-family-3-day,hongkong-layover-1-day,hongkong-macau-family-3-day}/itinerary.md`).
An external review (`/Users/bule-station/Downloads/roamweave_three_plan_review.md`)
read all three and concluded the skill is good at "generating a
plausible itinerary" but not yet at "proving the itinerary is
internally consistent, constraint-compatible, and executable." Two of
its findings were independently verified against the saved fixture
files (see `investigation.md`); the review's proposed fix is a broad
10-part "Planning Correctness Layer."

## Expected behavior

`travel-planner`'s existing self-check step
(`skills/travel-planner/references/quality-check.md`) catches the two
confirmed defect classes below before presenting output to the user,
using the same documentation-checklist mechanism it already uses for
everything else — not a new executable subsystem, unless investigation
shows the checklist mechanism genuinely can't carry the check.

## Current behavior

- No checklist item cross-checks a stated `Assumption` against the
  `Day` timeline it's supposed to inform — see Fact 2 in
  `investigation.md`.
- No checklist item validates traveler-location state
  (landside/security-cleared/airside/gate) across consecutive
  `TransitLeg`/`Stop` entries in a single day — see Fact 1.
- `docs/itinerary-data-model.md` already models `Constraint.kind`
  (hard/soft), `Assumption`, `ValidationIssue` (with `severity`), and
  `Source.checked_at`/`confidence` — much of the review's proposed
  data model already exists; the gap is in what `quality-check.md`
  actually asks the Agent to check, not in the schema.

## Scope

### In scope

- Re-verify each of the review's 10 proposed sub-areas against the
  three fixture files + the current schema/checklist, before deciding
  which are real gaps.
- For confirmed gaps: prefer extending `quality-check.md` (documentation
  checklist, matches existing architecture) over introducing new
  executable validation code.
- Revisit `docs/itinerary-data-model.md` only where a confirmed gap
  can't be expressed with existing entities (e.g. no structured
  family/accessibility fields exist today — everything lives in free-text
  `notes`).

### Out of scope

- The review's "render completeness validation" item — checked against
  the actual saved file and not substantiated (see `investigation.md`
  Fact 3). Not carried into this ticket unless new evidence appears.
- Any executable/code-based constraint solver, state machine, or
  topology-reasoning engine — `quality-check.md` line 3 establishes
  this checklist is deliberately documentation-only, applied by the
  Agent; building code here would be a bigger architecture change than
  three real-world fixtures justify. Revisit if this ticket's minimal
  fix proves insufficient on a future real trip.
- Booking, price comparison, live inventory — unchanged MVP non-goal
  (`docs/HANDOFF.md` §6.4).

## Repos / modules likely involved

| Area | Repo / module | Why it may be involved |
|---|---|---|
| Validation checklist | `skills/travel-planner/references/quality-check.md` | Primary candidate — checklist items are missing, not the data model |
| Data model | `docs/itinerary-data-model.md`, `schemas/itinerary.schema.json` | Only if a confirmed gap needs a new field (e.g. family/accessibility) |
| Regression fixtures | `tests/fixtures/{dalian-family-3-day,hongkong-layover-1-day,hongkong-macau-family-3-day}/` | Evidence base; may need a short "known issues" note per fixture |

## Links

- Ticket:
- PR:
- Staging URL:
- Production URL:
- Discussion thread:
- Design/spec: `/Users/bule-station/Downloads/roamweave_three_plan_review.md` (external review, source material — not an approved spec)

## Important constraints

- Do not edit code until ownership is confirmed.
- Keep the diff minimal.
- Avoid unrelated formatting.
- Verify every claim in the external review against the actual fixture
  files before acting on it — one claim (render completeness) already
  failed this check.

## Short title

Planning Correctness Layer design (from 3-plan review)
