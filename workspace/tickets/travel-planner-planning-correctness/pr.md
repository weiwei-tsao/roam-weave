# travel-planner-planning-correctness PR Notes

## PR title

```text
feat: add timeline/assumption consistency checks to travel-planner
```

## PR description

```md
## Summary
- Three real trips planned with `travel-planner` (now archived as
  `tests/fixtures/{dalian-family-3-day,hongkong-layover-1-day,
  hongkong-macau-family-3-day}/`) surfaced two internal-consistency
  defects an external review caught: a timeline that re-describes
  clearing airport security after already marking the traveler airside,
  and an `Assumption` ("arrives before noon") that contradicts the Day 1
  timeline it's supposed to inform (arrival shown at 14:00).
- Added two items to `skills/travel-planner/references/quality-check.md`
  — the single self-check gate every generation/edit already passes
  through — so both defect classes are caught before output is
  presented, without adding a new schema field or code layer.
- Investigated but deliberately did not build the external review's
  full "Planning Correctness Layer" proposal (constraint solver,
  topology reasoning, state-machine engine): `docs/itinerary-data-model.md`
  already models most of what the review proposed (`Constraint.kind`,
  `Assumption`, `ValidationIssue`, `Source.checked_at`/`confidence`), and
  `quality-check.md` is deliberately documentation-only by design — a
  code subsystem would be a bigger architecture change than three real
  trips justify. One review claim (output truncation) was checked
  against the actual saved fixture and found false; not carried forward.

## Test
- No automated runner applies — `quality-check.md` is a documentation
  checklist applied by the Agent, not executable code.
- Hand-verified both new items against all 3 real-trip fixtures: each
  flags exactly the defect it targets and stays silent on fixtures/days
  that don't exhibit that problem (table in
  `workspace/tickets/travel-planner-planning-correctness/test.md`).

## Notes
- Scope was deliberately kept to the two *confirmed* defects. The
  review's other claims (transport topology/open-jaw reasoning, vague
  transit-time blocks, pace-model concreteness, family/accessibility
  structuring) were not re-verified with the same rigor and are left as
  open follow-up candidates, not folded into this change — see
  `investigation.md` Open questions.
```

## Commit message

```text
feat: add timeline/assumption consistency checks to travel-planner
```

## Status update — ready for verification

```text
Ready for self-review. Added 2 quality-check.md items closing the two
confirmed defects from the 3-plan review; verified by hand against all
3 real-trip fixtures, no false positives.
```

## Status update — fixed and verified

```text
Verified: both new checklist items flag their target defect and stay
silent elsewhere across all 3 fixtures. No schema/code change needed.
```

## Status update — deployed

```text
Merged. quality-check.md now catches airport-security-state
contradictions and assumption/timeline mismatches before output is
presented.
```

## Reviewer notes

- Diff is additive-only (12 lines, one file) — nothing else changed.
- If this checklist-only fix later proves insufficient on a future real
  trip, the next escalation point is `docs/itinerary-data-model.md`
  (e.g. an explicit `Stop.security_state` field), not a code validator —
  re-open investigation before jumping there.
