# Destination Research

How to build a **Destination Brief** (`docs/itinerary-data-model.md` §2):
the full tiered candidate set for a destination, not scoped to any
particular trip's dates or length. If the destination comes up again
later in the same conversation, reuse what was already researched instead
of re-running this from scratch.

## Process

1. **Enumerate broadly.** Cover the destination's neighborhoods/areas, not
   just the handful of stops one trip could fit. Breadth here is what
   makes the brief reusable across different trip lengths and interests.

2. **Tier every candidate** — `must_see`, `recommended`, or `optional`
   (`docs/itinerary-data-model.md` §2 `Candidate.tier`):
   - `must_see`: broadly recognized as defining the destination; skipping
     it would be a notable gap in most visitors' experience.
   - `recommended`: strong value, but reasonable to skip under time
     pressure or if it doesn't match the traveler's interests.
   - `optional`: worthwhile only with extra time or a specific interest
     match (a particular architecture/food/history angle).

3. **Fill each candidate's fields:**
   - `name` / `local_name` — the local-language name must resolve in a
     map search (`docs/HANDOFF.md` §10); don't invent a display name that
     can't be looked up.
   - `category`, `area` — free text; group by neighborhood/geographic
     cluster so the Plan flow can build day-by-day areas later.
   - `description` — history, key eras, core experience. This is a
     **stable fact** (`docs/itinerary-data-model.md` §4): write it once,
     it shouldn't need a source citation.
   - `photography_note` — qualitative only (best light, angle, time of
     day). No numeric score — false precision doesn't help the traveler
     (`docs/INITIAL_FINDINGS.md` §8 principle 5).
   - `reservation`, `opening_hours`, `last_admission`, `estimated_cost` —
     **dynamic facts**. Every one of these requires a `sources` entry
     (url + `checked_at` + `confidence`). If you can't fully verify one,
     set `confidence: uncertain` and say so in the output — never present
     an unverified dynamic fact with the same confidence as a stable one.
   - `estimated_cost.currency` is mandatory whenever `amount` is set
     (`docs/HANDOFF.md` §5.4).

4. **Destination-level extras**: `cost_saving_tips` and `general_notes`
   (safety, scams, practical warnings) — keep these destination-wide, not
   duplicated per candidate.

## Optional: Xiaohongshu as a supplementary source

If the current environment already has an MCP tool available that can
search or read Xiaohongshu (小红书/RedNote) content, it may be used as a
supplementary discovery source alongside step 1 above. This document does
not name or recommend a specific tool — that decision belongs to whoever
configured the environment, not to this skill. If no such tool is
available, skip this entirely: do not suggest installing one, and do not
attempt to fetch Xiaohongshu content via `WebSearch`/`WebFetch` (its
anti-bot measures block that in practice).

**What it's for**: subjective, time-sensitive material plain web search
under-covers — local/lesser-known candidates, food recommendations,
photography angles, current popularity/trends. This feeds candidate
discovery and tiering (steps 1–2 above), not dynamic-fact verification.

**What it is not for**: a Xiaohongshu note is never sufficient on its own
to mark a dynamic fact (`reservation`, `opening_hours`, `last_admission`,
`estimated_cost`) `confidence: verified` — personal blogger content is
treated as unverified by default. If it's the only evidence for a dynamic
fact, record `confidence: uncertain` per the rule above and say so in the
output.

English-language social platforms are out of scope here — not yet decided,
tracked as a separate follow-on ticket.

## Output

Render with `templates/destination-brief.md`. This is Markdown-only this
milestone (`docs/itinerary-data-model.md` §1) — there is no
`destination-brief.schema.json` yet, so there's no structured-data step
between research and rendering here, unlike the Plan flow.
