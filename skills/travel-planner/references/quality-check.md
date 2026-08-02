# Quality Check

A documentation-only checklist, applied by the Agent — not executable
code (`docs/superpowers/specs/2026-08-01-travel-planner-mvp-design.md`
§7: revisit once the Markdown/schema contract has been used on a few real
trips and this checklist has stabilized).

Run through this after **every** generation or revision, before
presenting the result. For anything that fails, either fix it or add a
`ValidationIssue` entry (`docs/itinerary-data-model.md` §3) so it's
surfaced to the user rather than silently shipped.

## Checklist

- [ ] Every place name plausibly exists and its `local_name` resolves in
      a map search (`docs/HANDOFF.md` §10).
- [ ] No overlapping `arrival`/`departure` times between consecutive
      `stops` within a `Day`.
- [ ] No `Stop` scheduled outside its venue's known `opening_hours`.
- [ ] The last `Stop` of each `Day` has its `last_entry` checked, if the
      venue has one.
- [ ] Every `Stop` with `reservation_status: required` has a
      `reservation_url`.
- [ ] Every dynamic fact (`opening_hours`, `last_admission`,
      `estimated_cost`, `reservation_status`) has a `Source` with
      `checked_at`; anything `confidence: uncertain` is called out in the
      rendered output, not presented as settled.
- [ ] `Day.estimated_walk_km` is present and plausible given the day's
      stop count and area.
- [ ] No excessive backtracking — stop order roughly follows the area's
      geography, not a zigzag.
- [ ] Every `estimated_cost.amount` (Destination Brief) has a `currency`
      (`docs/HANDOFF.md` §5.4).
- [ ] Naming is consistent — the same place uses the same display name
      throughout a single document; local-language names appear once at
      first mention, per the writing convention referenced in
      `docs/HANDOFF.md` §3.5 (writing/naming conventions belong to the
      skill layer, not this checklist, but consistency is still checked
      here).
- [ ] Every `CandidateStatus` entry has a non-empty `reason` — especially
      any `must_see`-tier candidate that was excluded or demoted.
- [ ] Missing information is recorded as an `Assumption` with a `reason`,
      not silently filled in (`docs/HANDOFF.md` §6.2).
- [ ] Every `Day` has a non-empty `design_note`.
- [ ] The rendered output matches the structure of
      `templates/destination-brief.md` or `templates/itinerary.md`, as
      applicable.

## On revision

Only re-check the `Day`/`Stop` entries the edit touched, plus anything
downstream of them (e.g. a dropped `Stop` may free up time that shifts
the next stop's `arrival`). Don't re-run the full checklist against
unrelated days — see `route-planning.md`'s revision model.
