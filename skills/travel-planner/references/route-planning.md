# Route Planning

Core rules for turning a Destination Brief plus a traveler's constraints
into a `Trip` (`docs/itinerary-data-model.md` §3,
`schemas/itinerary.schema.json`), and for revising one that already
exists. Read `one-day-trip.md` or `multi-day-trip.md` alongside this file
for length-specific rules.

## Route design principles

(`docs/INITIAL_FINDINGS.md` §8, applied at the `Day`/`Stop` level)

1. **One primary area or theme per `Day`.** Avoid crossing the city
   repeatedly in a single day.
2. **Group geographically, minimize backtracking.** Order `stops` so the
   route flows through the area rather than zigzagging.
3. **Realistic transfer time.** Every gap between stops gets a
   `TransitLeg` with an honest `estimated_duration_minutes` — don't leave
   travel time implicit.
4. **Don't overfill the day.** Leave slack; a shorter coherent day beats a
   packed checklist.
5. **Restaurants are candidates, not anchors.** Populate `meal_options`
   near the day's stops; don't reroute the day around a specific
   restaurant unless food is a stated interest
   (`docs/INITIAL_FINDINGS.md` §8 principle 6).
6. **Distinguish must-reserve from recommended-to-reserve.**
   `Stop.reservation_status: required` must carry a `reservation_url`.
7. **Explain every exclusion.** Every `Candidate` from the brief gets a
   `CandidateStatus` entry with a non-empty `reason` — must-see items that
   didn't make the cut need a clear explanation, not silence
   (`docs/HANDOFF.md` §5.4).
8. **Respect opening hours and last admission.** The last stop of a `Day`
   must have `last_entry` checked if the venue has one.
9. **Estimate intensity qualitatively.** `estimated_walk_km` is
   approximate; `intensity` is `light`/`moderate`/`high`, not a
   fitness-tracker number.
10. **Write the `design_note` first.** One short paragraph per `Day`
    explaining the route logic, before the stop list — this is what lets
    the traveler sanity-check the day at a glance rather than parsing the
    stop table.
11. **Keep facts, assumptions, and recommendations distinguishable**
    throughout (`docs/itinerary-data-model.md` §4) — a `design_note` is a
    recommendation and should read like one, not like a verified fact.

Weather/fatigue/crowd fallback options don't get a dedicated entity in
this milestone — capture them as `Day.notes` or `Stop.notes` (e.g. "if
raining, swap for the covered market nearby").

## Revising an existing itinerary

The reference workflow this design is based on makes clear that the first
draft is never the final answer — the traveler revises against a map, and
the Agent's job is to recompute cleanly, not to regenerate everything
(`docs/superpowers/specs/2026-08-01-travel-planner-mvp-design.md` §6).
Never re-run generation from scratch for an edit.

When the user gives a natural-language edit — e.g. "move the museum to
the morning," "drop that shopping street," "I don't want an early start,"
"no touristy restaurants for lunch," "day 2 afternoon is too packed," "is
this worth a special trip," "don't change hotels," "keep this one but
make it a backup":

1. **Locate the affected slice** — which `Day`, `Stop`, or `TransitLeg`
   entries the edit touches.
2. **Apply the edit.**
3. **Recalculate only what's affected**: stop ordering, opening-hour
   conflicts, walking distance, geographic backtracking, reservation
   risk, day `intensity`. Don't touch days the edit didn't reach.
4. **Update `CandidateStatus`** if a candidate was added, dropped, or
   demoted to backup — `reason` must reflect the new state, not the old
   one.
5. **Update `Assumption.confirmed`** if the edit resolves something that
   was previously an assumption.
6. **Re-run `quality-check.md`** on the changed days.
7. **Report the delta** — what changed and why — not the full document
   restated.
