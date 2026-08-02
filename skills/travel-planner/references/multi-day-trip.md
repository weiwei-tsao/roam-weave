# Multi-Day Trip

Additions to `route-planning.md` specific to a `Trip` spanning multiple
`Day` entities.

- **A different primary `area` per `Day` where possible.** Repeating the
  same area on consecutive days should be a deliberate choice (e.g. the
  destination genuinely has one dense area with more than a day's worth
  of `must_see` candidates), not a default.
- **Anchor days near the accommodation.** If `Trip.accommodation.area` is
  known, prefer starting/ending each `Day` close to it — this is what
  keeps `transit_legs` honest instead of quietly assuming free travel
  back to the hotel every night.
- **Distribute `must_see` candidates across days by area**, not by
  cramming them into day 1. A traveler who has to cut the trip short
  should lose the least-important days, not the most important ones.
- **Vary `intensity` day to day.** Don't stack `high` on every day —
  fatigue accumulates across a multi-day trip in a way a single day
  doesn't need to account for.
- **Watch for day-of-week closures.** If `Trip.dates` is set, some
  `must_see` candidates may be closed on a specific weekday within the
  trip — this is a dynamic fact requiring a `Source`, and if it forces a
  day reassignment, explain it in that `Day.design_note`.
- **Check cross-day reservation conflicts.** A `Stop.reservation_status:
  required` entry with a fixed time can't be double-booked against
  another day's fixed commitment (e.g. a multi-day museum pass with a
  specific entry slot).
- **Keep the last day lighter**, accounting for departure transit
  (`Trip.departure`) — don't schedule a `must_see` candidate with a tight
  `last_entry` on a day that also needs to catch a departing train or
  flight.
