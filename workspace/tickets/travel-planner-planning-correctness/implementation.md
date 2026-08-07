# travel-planner-planning-correctness Implementation

## Current branch

```text
Repo: roam-weave
Branch: main
```

## Change scope

Add two checklist items to `skills/travel-planner/references/quality-check.md`
that would have caught the two confirmed defects from `investigation.md`
(Facts 1 and 2). No schema, template, or SKILL.md change — both fixes fit
the existing documentation-checklist mechanism.

## Files to change

| Repo | File | Intended change |
|---|---|---|
| roam-weave | `skills/travel-planner/references/quality-check.md` | Add 2 checklist items: (1) landside/airside state consistency across a day's transit, (2) Assumption-vs-timeline consistency |

## Implementation plan

1. Add a state-consistency item near the existing overlapping-times item
   (both are timeline-shape checks).
2. Add an assumption-consistency item near the existing "missing info →
   Assumption" item (both are about the `Assumption` entity).
3. Hand-verify both new items against all three fixtures: do they flag
   the two confirmed defects, and do they stay silent (no false positive)
   on the fixtures that don't have that problem?

## Actual changes made

- `skills/travel-planner/references/quality-check.md`: added the two
  checklist items described above (lines 19-25 and 51-55 of the updated
  file).

## Diff summary

- +11 lines, 0 removed, 1 file changed.

## Constraints followed

- [x] Minimal diff
- [x] No unrelated formatting
- [x] No broad refactor
- [x] Ownership confirmed (`quality-check.md`, per investigation.md)
- [x] Cross-module impact considered (schema/template left untouched —
      confirmed unnecessary in investigation.md Fact 4)

## Risks

- Low. Pure documentation addition to a checklist the Agent already reads
  before presenting output; no code path, no schema change, nothing that
  could break existing generation.

## Rollback notes

Revert the single commit touching `quality-check.md` — no other file
depends on the two new lines.
