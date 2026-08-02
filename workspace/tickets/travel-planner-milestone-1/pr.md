# travel-planner-milestone-1 PR Notes

## PR title

```text
feat: build travel-planner MVP Milestone 1 contract
```

`conventions.md`'s commit/PR style is Conventional Commits with **no
scope and no ticket reference** in the title (matches existing history:
"docs: add handoff doc") — this overrides the generic
`fix(<ticket-id>): ...` placeholder that was in this template by default.
`feat`, not `fix`: this ticket builds new capability, it doesn't fix a
defect.

## PR description

```md
## Summary
- Implements the design-and-contract Milestone 1 for the `travel-planner`
  skill, per `docs/superpowers/specs/2026-08-01-travel-planner-mvp-design.md`
  §5: `docs/itinerary-data-model.md`, `schemas/itinerary.schema.json` +
  `schemas/traveler-profile.schema.json`, `skills/travel-planner/SKILL.md`
  + 5 reference files, 3 templates (2 Markdown + 1 single-file HTML), and
  2 end-to-end fixtures (oxford-one-day, edinburgh-family-3-day).
- Adds the design spec itself, produced by brainstorming a reference
  Codex travel-workflow writeup against `docs/HANDOFF.md`'s MVP
  non-goals.
- Syncs `workspace/ecosystem.md`'s repo-layout doc, which had drifted
  stale mid-build (caught by `/code-review`'s Standards axis).

## Test
- Both schemas validated as well-formed JSON Schema Draft 2020-12, and
  checked against hand-built valid/invalid instances (missing required
  field, extra property, bad enum value) — all correctly
  accepted/rejected.
- Both fixtures' `itinerary.json` validate against
  `schemas/itinerary.schema.json`.
- Cross-checked every `Stop.candidate_id` / `CandidateStatus.candidate_id`
  in both fixtures against the corresponding `destination-brief.md`
  headings — full 1:1 coverage, no orphans in either direction.
- `edinburgh-family-3-day/itinerary.html` checked for balanced/well-formed
  HTML tags.
- Ran `/code-review` (Standards + Spec axes) against `main...HEAD`: Spec
  axis — 0 findings, full compliance with the design spec. Standards axis
  — 2 minor findings (stale `ecosystem.md`, now fixed on this branch;
  two over-length commit messages, left as-is — pre-existing commits).
- Per `conventions.md` "Stages / acceptance": re-read `docs/HANDOFF.md`
  §6.4 (MVP non-goals) before merging — confirmed via the Spec-axis
  review that scope wasn't quietly expanded (no booking, maps, museum/
  photography modules, or executable validator code).

## Notes
- No executable code in this milestone by design —
  `quality-check.md` stays a documentation-only checklist (design spec
  §7); revisit once it's been used on a few real trips.
- `destination-brief.schema.json` intentionally not built this milestone
  — Destination Briefs stay Markdown-only
  (`docs/itinerary-data-model.md` §1).
- Docs/schema/skill-content diff only — no application code, no new
  dependencies.
```

## Commit message

```text
feat: build travel-planner MVP Milestone 1 contract
```

For the squash-merge into `main` (this repo's established merge pattern
— PR #1 was squash-merged per `.handoff/HANDOFF.md`), this is the single
commit message that would represent the whole branch.

## Status update — ready for verification

```text
travel-planner-milestone-1 ready for self-review on
feat/travel-planner-milestone-1. All 7 Milestone 1 deliverables
complete; both fixtures validate against schemas/itinerary.schema.json;
/code-review (Standards + Spec) run clean (Spec: 0 findings, Standards:
2 minor, both resolved or accepted).
```

This repo has no staging/UAT environment
(`conventions.md` "Stages / acceptance": self-review only) — adapted
from the template's `<env>`/`<result>` placeholders accordingly.

## Status update — fixed and verified

```text
Self-reviewed and accepted. Spec-axis review confirms the implementation
matches docs/superpowers/specs/2026-08-01-travel-planner-mvp-design.md
§5 with no scope creep against docs/HANDOFF.md §6.4.
```

(Adapted from "fixed and verified" bug-fix language — this ticket built
new capability, there was no defect to fix.)

## Status update — deployed

```text
Merged to main. No separate deploy step — this milestone is
documentation/schema/skill content, consumed directly from the repo by
whichever agent invokes the travel-planner skill next.
```

## Reviewer notes

- Solo project, self-review only — no external reviewer
  (`conventions.md` "Stages / acceptance").
- Almost everything in this PR is new file additions (`git diff
  main...HEAD`: 28 files changed, 2280 insertions(+), 12 deletions(-)) —
  the only pre-existing file touched is `workspace/ecosystem.md`
  (12 lines changed, to fix the stale repo-layout doc caught by
  `/code-review`).
- If reviewing later with fresh eyes: start from
  `docs/itinerary-data-model.md` (everything else references its entity
  names), then spot-check one fixture end-to-end against
  `skills/travel-planner/SKILL.md`'s routing before trusting the rest.
