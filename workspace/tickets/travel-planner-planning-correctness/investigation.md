# travel-planner-planning-correctness Investigation

## Reproduction steps

1. Read `tests/fixtures/hongkong-layover-1-day/itinerary.md` lines 20-33 (the
   Day 1 stop/transit table).
2. Read `tests/fixtures/hongkong-macau-family-3-day/itinerary.md` lines 5-19
   (Assumptions section + Day 1 table).
3. Compare each fixture's actual text against the corresponding claim in
   `/Users/bule-station/Downloads/roamweave_three_plan_review.md`.

## Facts

<!-- Evidence-backed observations only. -->

- **Fact 1 — timeline state contradiction (confirmed).**
  `tests/fixtures/hongkong-layover-1-day/itinerary.md` line 31: `~01:15 |
  抵达机场，过安检进入禁区` (security cleared, entered restricted/airside
  zone), then line 33: `05:00–05:30 | 前往值机 / 安检 / 登机口` (go to
  check-in/security/gate) — re-mentions security clearance for a traveler
  the document already placed airside 3h45m earlier. No entity in
  `docs/itinerary-data-model.md` tracks a Stop's landside/airside state, so
  nothing would have caught this contradiction mechanically.
- **Fact 2 — assumption contradicts its own timeline (confirmed).**
  `tests/fixtures/hongkong-macau-family-3-day/itinerary.md` line 7: the
  Assumptions section states "假设9月22日**中午前**抵达香港" (assume
  arrival before noon), but line 19's Day 1 table starts `14:00–15:30 |
  抵达香港国际机场 → 酒店` (arrival at 14:00 = 2pm). The stated assumption
  and the generated timeline directly disagree. `quality-check.md`'s
  checklist has no item that cross-checks an `Assumption.description`
  against the `Day` timeline it's supposed to inform.
- **Fact 3 — "render completeness" claim NOT substantiated (reviewed and
  rejected).** The review's §3.6 quotes truncated fragments ("小朋",
  "反之亦", "视 亭前地", "作为下次专程去澳门时") as evidence of generation
  defects. Grepped `tests/fixtures/hongkong-macau-family-3-day/itinerary.md`
  directly: the corresponding sentences are complete — "视小朋友状态选择性进行"
  (line 40), "反之亦可" (line 48), "议事亭前地" (line 52/60/64/66/77),
  "作为下次专程去澳门时的选项保留" (line 81). This claim does not hold
  against the actual saved file; likely a rendering artifact in the
  reviewer's own environment, not a `travel-planner` defect. Not carried
  forward into scope (see `context.md` Out of scope).
- **Fact 4 — much of the proposed data model already exists.**
  `docs/itinerary-data-model.md` §3 already defines: `Constraint.kind`
  (`hard | soft`), `Assumption` (with a `confirmed` boolean), `ValidationIssue`
  (`severity: blocker | warning | note`, `resolved: boolean`), and `Source`
  (`checked_at` + `confidence: verified | uncertain`). The review's
  "constraint classification," "assumption tracking," and "dynamic-fact
  freshness" proposals largely restate entities that already exist — the
  gap is that `quality-check.md` doesn't yet instruct the Agent to *use*
  `ValidationIssue`/`Assumption` to catch cases like Facts 1-2, not that the
  schema is missing them.
- **Fact 5 — family/accessibility info is genuinely unstructured.**
  All three fixtures carry stroller/nap/stairs information only as free-text
  `notes` embedded in `Day`/`Stop` prose (e.g. dalian fixture: "部分场馆入口有台阶，婴儿车通行需留意"
  inline in a table cell). `docs/itinerary-data-model.md` has no structured
  field for this — `Trip.travelers.notes` is the closest, and it's a
  string array, not a checklist. This part of the review's proposal is a
  real, unaddressed gap.
- **Fact 6 — `quality-check.md` line 3 explicitly anticipates this
  moment.** The file's own header comment says the checklist should be
  "revisit[ed] once the Markdown/schema contract has been used on a few
  real trips and this checklist has stabilized" — three real trips now
  exist. It also explicitly states the checklist is "documentation-only ...
  not executable code," which is a standing architecture decision, not an
  oversight.

## Hypotheses

<!-- Guesses that still need confirmation. -->

- The other review claims not yet individually re-verified (transport
  topology / open-jaw reasoning in the HK+Macau fixture, the "21:30-00:20"
  vague transit block, pace-model concreteness, derived-assumption
  chaining) are plausible based on a first read but haven't been checked
  with the same line-level rigor as Facts 1-3 — needs a pass before
  committing to fixing all of them.
- Fixing Facts 1 and 2 by adding two checklist items to `quality-check.md`
  is likely sufficient to close the two *confirmed* defects without any
  schema or code change — unverified until the checklist is actually
  drafted and run against the three fixtures.

## Decisions

<!-- Confirmed decisions made during investigation. -->

- Do not carry forward the review's "render completeness validation"
  item — Fact 3 shows the underlying claim is false.
- Do not build an executable constraint-solver / state-machine / topology
  engine as the review's Milestone 2 proposal suggests — `quality-check.md`
  is deliberately documentation-only (Fact 6); a code subsystem is a much
  bigger architecture change than three fixtures justify. Default to
  extending the existing checklist mechanism unless a specific gap proves
  it can't be expressed that way.
- Family/accessibility structuring (Fact 5) is a real gap but is a
  separate, larger design question (what fields, how they render, whether
  they're worth a `docs/itinerary-data-model.md` change) — worth a
  follow-up decision, not folded silently into the same minimal fix as
  Facts 1-2.

## Data / component flow

```text
User itinerary request
→ skills/travel-planner/references/route-planning.md +
  one-day-trip.md / multi-day-trip.md (generation)
→ skills/travel-planner/references/quality-check.md (self-check, BEFORE
  presenting output) ← Facts 1-2 show this step currently has no item
  that would have caught either contradiction
→ Markdown rendered from templates/itinerary.md
```

## Files inspected

| Repo | File | Why inspected | Finding |
|---|---|---|---|
| roam-weave | `tests/fixtures/hongkong-layover-1-day/itinerary.md` | Verify review's timeline-contradiction claim | Confirmed (Fact 1) |
| roam-weave | `tests/fixtures/hongkong-macau-family-3-day/itinerary.md` | Verify review's assumption-mismatch and truncation claims | Assumption mismatch confirmed (Fact 2); truncation claim rejected (Fact 3) |
| roam-weave | `docs/itinerary-data-model.md` | Check what data model already supports before accepting review's proposed model | Constraint/Assumption/ValidationIssue/Source already exist (Fact 4); no family/accessibility fields (Fact 5) |
| roam-weave | `skills/travel-planner/references/quality-check.md` | Check current checklist coverage | Documentation-only by design (Fact 6); no assumption-consistency or timeline-state items exist |

## Root cause

Two confirmed defects (Facts 1, 2) trace to the same root cause:
`quality-check.md`'s checklist — the single self-check gate every
generation/edit passes through before being shown to the user — has no
item that (a) cross-checks stated `Assumption`s against the `Day` timeline
they're supposed to inform, or (b) tracks a traveler's
landside/security-cleared/airside state across a day's stops/transit legs.
Both are missing checklist items, not missing data-model entities or a
missing code layer — `Assumption`, `ValidationIssue`, and `Constraint` all
already exist in `docs/itinerary-data-model.md` §3.

## Owner repo / module

`skills/travel-planner/references/quality-check.md` (single-repo project;
this is the correct layer per `workspace/ecosystem.md`'s ownership table:
"Planning logic / route mistakes" → `references/*` first).

## Checked but not responsible

- `docs/itinerary-data-model.md` / `schemas/itinerary.schema.json` — already
  has the entities needed for Facts 1-2; not the root cause for those two.
  Would only become responsible if Facts 1-2's fixes turn out to need a new
  field (e.g. an explicit `Stop.security_state` enum) rather than a
  checklist instruction the Agent can apply through the existing free-text
  `notes`/`design_note` mechanism.

## Evidence

- Fact 1: `tests/fixtures/hongkong-layover-1-day/itinerary.md:31,33`
- Fact 2: `tests/fixtures/hongkong-macau-family-3-day/itinerary.md:7,19`
- Fact 3: `tests/fixtures/hongkong-macau-family-3-day/itinerary.md:40,48,52,81`
- Fact 4/6: `docs/itinerary-data-model.md` §3; `skills/travel-planner/references/quality-check.md:3`

## Open questions

- Should the two new checklist items (assumption-consistency,
  airside/landside state) be general-purpose (apply to any itinerary) or
  scoped narrowly to "arrival/departure timing" and "airport/border
  transit days"? Affects how broadly `quality-check.md` needs to change.
- Is a `Stop.security_state`-style field ever needed, or can "don't
  re-mention security clearance after the traveler is marked airside" be
  expressed as a checklist instruction the Agent applies to its own
  generated prose without a new schema field? Leaning toward the latter
  (smaller diff) but not yet confirmed.
- Does the review's remaining unverified material (topology/open-jaw,
  vague transit blocks, pace-model concreteness, family/accessibility
  structuring) belong in this ticket's Phase 2, or should each become its
  own follow-up ticket once Facts 1-2 are fixed and re-tested against the
  three fixtures? Leaning toward: fix Facts 1-2 first (small, confirmed,
  same mechanism), then re-evaluate scope for the rest as separate
  tickets rather than one large batch.

## Next investigation steps

- If proceeding to Phase 2 now: draft the two `quality-check.md` checklist
  items for Facts 1-2 and hand-check them against all three fixtures
  (would they have actually flagged the two confirmed defects?) before
  calling the checklist change done.
- If broader scope is wanted: re-verify the review's remaining claims
  (topology, transit-block vagueness, pace concreteness) with the same
  line-level rigor as Facts 1-3 before adding them to scope.
