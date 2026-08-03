# Xiaohongshu Supplementary Source — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add an "optional Xiaohongshu supplementary source" subsection to the travel-planner skill's Discover-flow research reference, per the approved spec.

**Architecture:** Single-file documentation edit. No code, no schema, no new dependency — a new `##` subsection inserted into `skills/travel-planner/references/destination-research.md`, between the existing `## Process` and `## Output` sections.

**Tech Stack:** Markdown only.

## Global Constraints

(from `docs/superpowers/specs/2026-08-02-social-media-sourcing-design.md`)

- No specific MCP project/tool named or recommended in the skill doc.
- No login/session/cookie handling logic anywhere in `travel-planner`.
- No English-language platform equivalent in this ticket — explicitly deferred.
- Does not reverse `docs/HANDOFF.md`'s "no APIs/scraping integrations yet" or "keep planning logic source-agnostic" non-goals — this is conditional guidance for an Agent, not an integration.
- A single Xiaohongshu note is never sufficient alone to mark a dynamic fact `confidence: verified` (must degrade to `confidence: uncertain` per the existing rule at `destination-research.md` lines 36–40).

---

### Task 1: Add the "Optional: Xiaohongshu as a supplementary source" subsection

**Files:**
- Modify: `skills/travel-planner/references/destination-research.md:44-48`

**Interfaces:**
- Consumes: the existing dynamic-fact rule already present in this file (lines 36–40: every dynamic fact requires a `sources` entry with `confidence`, `uncertain` when unverified).
- Produces: nothing consumed by other tasks — this is the only task in this plan.

- [ ] **Step 1: Read the current file to confirm line numbers still match**

Run: `sed -n '30,54p' skills/travel-planner/references/destination-research.md`

Expected output (confirm before editing — if it differs, locate the `## Process` numbered list's step 4 and the `## Output` heading by content instead of line number):

```
   - `estimated_cost.currency` is mandatory whenever `amount` is set
     (`docs/HANDOFF.md` §5.4).

4. **Destination-level extras**: `cost_saving_tips` and `general_notes`
   (safety, scams, practical warnings) — keep these destination-wide, not
   duplicated per candidate.

## Output

Render with `templates/destination-brief.md`. This is Markdown-only this
milestone (`docs/itinerary-data-model.md` §1) — there is no
`destination-brief.schema.json` yet, so there's no structured-data step
between research and rendering here, unlike the Plan flow.
```

- [ ] **Step 2: Insert the new subsection between `## Process` and `## Output`**

Using the Edit tool, replace:

```
4. **Destination-level extras**: `cost_saving_tips` and `general_notes`
   (safety, scams, practical warnings) — keep these destination-wide, not
   duplicated per candidate.

## Output
```

with:

```
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
```

- [ ] **Step 3: Verify the edit against the spec's constraints**

Run: `grep -n "xiaohongshu-mcp\|login\|cookie\|session" skills/travel-planner/references/destination-research.md`

Expected: no matches (confirms no specific tool named, no login/session logic described — both are hard constraints from the spec).

Run: `grep -n "confidence: verified\|confidence: uncertain" skills/travel-planner/references/destination-research.md`

Expected: the new subsection's `confidence: verified` / `confidence: uncertain` references appear, and they read consistently with the existing rule earlier in the same file (lines 36–40) — no contradiction (e.g. the new text must not imply a Xiaohongshu note alone can satisfy `verified`).

- [ ] **Step 4: Read the full file once to confirm it reads cleanly end to end**

Run: `cat -n skills/travel-planner/references/destination-research.md`

Confirm: heading hierarchy is consistent (`## Process`, `## Optional: Xiaohongshu as a supplementary source`, `## Output` all at the same `##` level), no duplicated content, no broken cross-references.

- [ ] **Step 5: Stage the change (do not commit — this repo's convention is the user commits)**

```bash
git add skills/travel-planner/references/destination-research.md
git status
```

Expected: the file shows as staged modified; no other files touched by this task.
