# travel-planner-planning-correctness Test Plan

No automated test runner applies — `quality-check.md` is a
documentation-only checklist applied by the Agent, not executable code
(`tests/` only contains `fixtures/`, no runner). Verification is manual:
hand-walk both new checklist items against the three fixtures.

## Local checks

- [x] `git status` checked before testing (clean except this ticket's
      untracked workspace dir)
- [ ] Typecheck — N/A, Markdown only
- [ ] Unit tests — N/A, no code changed
- [ ] Lint — N/A
- [x] Manual hand-check of both new checklist items against all 3 fixtures

## Manual verification

| Checklist item | Fixture | Expected | Result |
|---|---|---|---|
| Landside/airside state consistency | `hongkong-layover-1-day` | Flags — 01:15 "过安检进入禁区" then 05:00 "前往值机/安检" re-describes clearing security with no distinct second checkpoint | ✅ Flags |
| Landside/airside state consistency | `dalian-family-3-day` | Silent — no airport-security narrative present in the doc | ✅ Silent (no false positive) |
| Landside/airside state consistency | `hongkong-macau-family-3-day` | Silent on Day 1 (no security-state prose); Day 3's two border crossings (HK→Macau, Macau→HK) are legitimately distinct checkpoints, explicitly excepted by the item's wording | ✅ Silent (no false positive) |
| Assumption-vs-timeline consistency | `hongkong-macau-family-3-day` | Flags — Assumptions says "中午前抵达" (before noon), Day 1 table shows 14:00 arrival | ✅ Flags |
| Assumption-vs-timeline consistency | `hongkong-layover-1-day` | Silent — no assumption states a specific time/date that the timeline contradicts | ✅ Silent (no false positive) |
| Assumption-vs-timeline consistency | `dalian-family-3-day` | Silent — assumptions (accommodation area, flight duration) aren't contradicted elsewhere in the doc | ✅ Silent (no false positive) |

Both items flag exactly the two confirmed defects from `investigation.md`
and stay silent on the fixtures/days that don't exhibit that problem —
no false positives observed across the 3-fixture sample.

## Environment checks

N/A — documentation-only change, no runtime environment.

## Regression checks

- [x] Existing checklist items unchanged (diff is additive only)
- [x] No unrelated area affected — single file, `quality-check.md`

## Acceptance

| Person | Role | Status | Date | Notes |
|---|---|---|---|---|
| weiwei cao | Owner | Pending | | Self-review only, per `conventions.md` — no external approver at this stage |

## Production deploy check

- [ ] Accepted on staging — N/A, no staging env for this repo
- [x] Safe for production — documentation-only, low risk
- [ ] Deployed — pending user's own commit (see `handoff.md`)
- [ ] Ticket marked done
- [ ] Status update sent, if needed

## Final test notes for PR

```text
Manually verified both new quality-check.md items against all 3 real-trip
fixtures (tests/fixtures/{dalian-family-3-day,hongkong-layover-1-day,
hongkong-macau-family-3-day}/itinerary.md): each flags exactly the
confirmed defect it targets and stays silent elsewhere. No automated
test applies — quality-check.md is a documentation-only checklist by
design.
```
