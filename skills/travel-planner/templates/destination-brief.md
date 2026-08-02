<!--
Template for a Destination Brief (docs/itinerary-data-model.md §2).
{{double-brace}} = fill with the DestinationBrief/Candidate field of that
name. <!-- comments --> mark repeated blocks or optional sections; both
kinds of markup are for the Agent's use when rendering — strip them from
the final output, they are not meant to reach the reader.
-->

# {{destination}}（{{local_name}}）

{{summary}}

## Must-See

<!-- one block per Candidate where tier == must_see, in area/geographic order -->

### {{name}}（{{local_name}}）

{{description}}

- **Category / area:** {{category}} — {{area}}
- **Photography:** {{photography_note}}
- **Reservation:** {{reservation}}
- **Hours:** {{opening_hours}} <!-- append "· last admission {{last_admission}}" if set -->
- **Cost:** {{estimated_cost.amount}} {{estimated_cost.currency}} <!-- omit line if no cost -->
- **Source:** {{sources[].url}} (checked {{sources[].checked_at}}<!-- append ", unverified" if confidence == uncertain -->)
<!-- render each note in Candidate.notes as its own bullet -->

<!-- end repeated block -->

## Recommended

<!-- same per-candidate structure as Must-See, tier == recommended -->

## Optional

<!-- same per-candidate structure as Must-See, tier == optional -->

## Cost-Saving Tips

<!-- one bullet per DestinationBrief.cost_saving_tips entry -->

## General Notes

<!-- one bullet per DestinationBrief.general_notes entry -->

---

Generated {{generated_at}}. Dynamic facts (hours, prices, reservations)
are current as of the source dates shown above — re-check anything marked
unverified, and re-verify all of it if your trip is far in the future.
