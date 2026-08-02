# One-Day Trip

Additions to `route-planning.md` specific to a single-`Day` `Trip`.

- **One `Day` entity.** No cross-day continuity to manage.
- **Weight toward `must_see`.** With only one day, `recommended` items
  are filler if time allows; `optional` items are rarely included —
  reflect this in each `CandidateStatus.reason`.
- **Anchor on arrival/departure.** If this is a day trip from another
  base, `Trip.arrival`/`departure` transit windows constrain the whole
  day — build the route to fit inside them, not the other way around.
- **Stop count is a function of duration and area, not a fixed number.**
  A compact historic core might fit 5-6 stops; a spread-out destination
  might realistically fit 3. Let `estimated_walk_km` and `intensity`
  reflect the actual route, don't force a stop count to look thorough.
- **`design_note` carries the day's single logic thread** — since there's
  only one `Day`, this paragraph is effectively the whole trip's
  rationale. Make it earn that.
- **Last stop's `last_entry` is critical** — there's no next day to
  recover a missed closing time on.
