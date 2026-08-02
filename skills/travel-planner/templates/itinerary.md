<!--
Template for a Trip Itinerary (docs/itinerary-data-model.md §3,
schemas/itinerary.schema.json). {{double-brace}} = fill with the field of
that name. <!-- comments --> mark repeated blocks/conditionals for the
Agent's use — strip them from the final output.
-->

# {{trip title}} — {{destination}}

{{dates or "Day 1–N" if dates unknown}} · {{travelers.count}} traveler(s) · {{pace}} pace

<!-- if assumptions is non-empty: surface them before the day-by-day plan,
     so the reader knows what to double-check before trusting it -->

## Assumptions

<!-- one bullet per Assumption: "{{description}} — {{reason}}" -->

<!-- repeat the following section once per Day, in day_number order -->

## Day {{day_number}} — {{area}} <!-- append " ({{date}})" if date is set -->

{{design_note}}

**Estimated walk:** {{estimated_walk_km}} km · **Intensity:** {{intensity}}

| Time | Stop | Notes |
|---|---|---|
<!-- one row per Stop, in order: -->
| {{arrival}}–{{departure}} | {{name}}（{{local_name}}） | {{reservation_status}}<!-- append " — book: {{reservation_url}}" if required --><!-- append " — last entry {{last_entry}}" if this is the day's last stop and last_entry is set --> |

**Getting there:** <!-- one clause per TransitLeg: "{{from}} → {{to}} ({{mode}}, ~{{estimated_duration_minutes}} min)", joined with " · " -->

**Nearby meal options:** <!-- one clause per MealOption: "{{name or area}} — {{note}}", joined with " · "; omit section if meal_options is empty -->

<!-- render Day.notes as bullets if non-empty -->

<!-- end repeated Day section -->

## Included / Excluded

| | Place | Reason |
|---|---|---|
<!-- one row per CandidateStatus, must_see tier first: "✅" or "❌" | {{candidate name}} | {{reason}} -->

<!-- if validation_issues has any unresolved entries, include this section; omit otherwise -->

## Open Questions

<!-- one bullet per unresolved ValidationIssue: "{{description}} ({{location}})" -->

---

Generated from the Destination Brief for {{destination}}. Anything marked
as an assumption above was not stated by the traveler — confirm or
correct it before relying on this plan.
