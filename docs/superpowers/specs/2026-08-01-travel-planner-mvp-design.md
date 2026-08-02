# travel-planner MVP Design (Milestone 1)

**Status:** Approved by user, pending write-up into implementation plan
**Date:** 2026-08-01
**Supersedes/refines:** `docs/HANDOFF.md` §2, §3.6, §8 (does not replace those documents — this spec is the concrete design that fills in their open questions before implementation starts)

## 1. Context

`docs/INITIAL_FINDINGS.md` and `docs/HANDOFF.md` establish the product direction (planning before booking, structured data before rendering, human-in-the-loop revision) but leave the concrete shape of Milestone 1 open. This spec was produced by reviewing a detailed first-person account of an existing Codex-based personal travel-planning workflow (referenced informally in `docs/HANDOFF.md` §5, full detail supplied by the user in this design session) and reconciling it against the MVP boundaries already committed in `docs/HANDOFF.md` §6.4.

The reference workflow's author solves the same problem RoamWeave is targeting, but does so manually: self-maintained prompt templates, self-reviewed map, self-discovered omissions, self-issued verbal corrections, self-judged completion. The opportunity for RoamWeave is to productize those steps, not to copy the author's full feature surface (which includes hotel/tour booking automation, multi-platform price comparison, and generated maps — all explicitly out of scope here).

No repository code exists yet. `main` and this feature branch are identical; only product-discovery docs and an unrelated `workspace/` ticket-tracking scaffold have been committed so far (see `.handoff/HANDOFF.md`).

## 2. Updated Product Definition

> RoamWeave is a human-in-the-loop personalized travel-planning agent. It first helps the user build understanding of a destination, then weaves preferences, constraints, and verified information into an explainable, editable, validated travel route.

This replaces the more generic statement in `docs/HANDOFF.md` §2 for the purposes of this milestone's writing (SKILL.md, etc.); it does not require editing the existing HANDOFF doc.

## 3. Process Model

### 3.1 Five-stage pipeline (refines `docs/HANDOFF.md` §3.6)

```text
Research → Normalize → Plan → Validate → Render
```

`docs/HANDOFF.md` §3.6 had "Research → Normalized data → Structured itinerary data → Validation → Rendering" as four conceptual steps; this spec makes **Plan** and **Validate** explicit, separate stages because they have different owners (Plan is generative, Validate is a checklist pass) and different failure modes.

### 3.2 Six-stage product-capability model (MVP covers the first five)

```text
Discover → Select → Plan → Refine → Present         [MVP]
                                       ↓
                                 Search / Book        [explicitly out of scope]
```

| Stage | Question answered | Artifact |
|---|---|---|
| Discover | What exists at the destination? | Destination Brief |
| Select | What fits this traveler? | Tiered/filtered candidate list |
| Plan | How should the trip be organized? | Itinerary draft |
| Refine | Does this match personal preference? | Human-revised itinerary |
| Present | How is this used while traveling? | Markdown + single-file HTML |
| Search/Book | What should be reserved? | *(not built — future phase)* |

Refine is first-class, not a cleanup pass after generation. The reference workflow's central lesson — "the AI-designed route is only a draft for reference" — means RoamWeave must support natural-language incremental edits with localized recalculation, not a single "Generate itinerary" action with no iteration path (see §6).

## 4. Three-Layer Rule/Data Boundary

The reference workflow bundles user preferences, formatting rules, and planning method into one large `Agents.md`. That produced the exact failure the author reports ("too many dense constraints → attention loss / omissions"). RoamWeave separates by **scope of change and data sensitivity**, not by arbitrary file size:

| Scope | Lives in | Contains real personal data? | Changes how often |
|---|---|---|---|
| Product Methodology | `skills/travel-planner/references/*.md` | No — ships with the skill | Rarely (design iteration) |
| User Preferences | `schemas/traveler-profile.schema.json` | No — schema/template only; real filled-in preferences are never committed to the repo (privacy boundary, `docs/HANDOFF.md` §10) | Per-user, out of repo |
| Trip Constraints | The `trip` / `constraint` entities inside `schemas/itinerary.schema.json` (already anticipated in `docs/HANDOFF.md` §8 item 2) | Ephemeral session input (dates, companions, hotel, must-see list) | Per planning session |

No new top-level `profile/` directory is created. This is a documentation/organization decision, not a new artifact.

## 5. Milestone 1 Deliverables

Refines the list in `docs/HANDOFF.md` §8 with the field-level detail validated against the reference workflow:

1. **`skills/travel-planner/SKILL.md`**
   - Two natural-language-triggered flows: destination research/enumeration (Discover) and route generation (Plan+Refine). No slash-command or CLI parameter parsing (`/roam plan --days 1` style stays out of scope) — trigger phrases only, resolved by the Agent's own understanding, same as the reference workflow's four-word invocations.
   - Routes to reference files rather than inlining rules.

2. **`docs/itinerary-data-model.md`**
   - **Destination Brief** entity: tiered candidates (must-see / recommended / optional-if-time), each with photography value, historical/experiential context, reservation requirement, opening hours, cost, source + `checked_at`. Not bounded by trip length — meant to be reusable across multiple trips to the same destination.
   - **Trip Itinerary** entities: `trip`, `day`, `stop`, `transit leg`, `meal option`, `source`, `constraint`, `assumption`, `validation issue`. Itinerary output ends with a ✅/❌ list of every candidate from the Destination Brief showing inclusion/exclusion, so removed must-see items are never silently dropped (this directly implements `docs/HANDOFF.md` §5.4's "missing explanation for removed must-see candidates" validation rule).
   - Explicitly out: no museum-specific sub-entities (exhibit halls, visit order, bilingual signage), no photography-specific scoring beyond a general note field. These are the reference workflow author's personal-interest modules, not core to the product.

3. **`schemas/itinerary.schema.json`**, **`schemas/traveler-profile.schema.json`** — practical first-pass JSON Schemas matching the data model above. Schema/template only, no real data committed.

4. **Planning references** (`skills/travel-planner/references/`):
   - `destination-research.md`
   - `route-planning.md` — includes the revision/recalculation model (§6 below)
   - `one-day-trip.md`
   - `multi-day-trip.md`
   - `quality-check.md` — a **documentation-only checklist**, applied by the Agent after generation/revision. No executable validator script in this milestone (confirmed decision — see §7).

5. **Markdown templates**: Destination Brief, Trip Itinerary — plus **one single-file HTML template** as the baseline Present-stage output (no interactive map, no multi-page site, no deploy tooling — just a self-contained HTML render of the same structured data as the Markdown).

6. **≥2 fixtures**: one simple one-day trip, one multi-day/family trip with meaningful constraints (mirrors the reference workflow's Oxford day-trip vs. Edinburgh 3-day-trip split in complexity).

## 6. Human-in-the-Loop Revision Model

Not "regenerate the whole document." The Agent must be able to take natural-language edit requests (examples drawn directly from the reference workflow's actual verbal-revision phrasing: "move the museum to the morning," "drop that shopping street," "I don't want an early start," "no influencer restaurants for lunch," "day 2 afternoon is too packed," "is this worth a special trip," "don't change hotels," "keep this spot but make it a backup") and:

1. Locate the affected structured-data slice (day / stop / ordering).
2. Recalculate only what the edit affects: stop ordering, opening-hour conflicts, walking distance, geographic backtracking, reservation risk, daily intensity.
3. Re-run the `quality-check.md` checklist.
4. Output Draft N+1 with a short explanation of what changed and why.

This behavior is documented in `route-planning.md` and `quality-check.md` — it is a process rule for the Agent to follow within a conversation, not application code (this repo ships a Claude Skill, not a service with persisted session state).

## 7. Validation: Documentation Checklist, Not Executable Code (explicit decision)

Considered and rejected for this milestone: writing an actual validator script (Python/Node, JSON Schema + rule engine, structured validation-report output). Reasons:
- `docs/HANDOFF.md` §8 item 7 already allows a documentation-only checklist for Milestone 1.
- The repo currently has zero runtime/tooling dependencies; adding a validator script would be the first executable code in the repo, which is a bigger commitment than this milestone's "design-and-contract" framing calls for.
- Revisit once the Markdown/schema contract has been used on a few real trips and the checklist has stabilized.

## 8. Non-Goals (reaffirms `docs/HANDOFF.md` §6.4, now with evidence from the reference workflow)

Confirmed still out of scope, even though the reference workflow demonstrates all of these work well in practice:
- Hotel/tour-group price comparison and automatic favoriting on Ctrip/Agoda/Booking/Airbnb, or any booking-platform account interaction.
- PDF comparison reports.
- `destination-map.md` / `itinerary-map.md` / `museum-map.md` style generated interactive maps.
- Museum-specific and photography-specific planning modules.
- Publishing/deploy tooling (e.g. Netlify).

Rationale (from this design session): none of these solve the reference workflow author's own stated core problem — "how do I actually want to move through this place, and what's worth cutting" — which is exactly what Discover/Plan/Refine addresses. Booking-layer capabilities are additive value on top of a working planner, not a prerequisite for one.

## 9. Definition of Done

Unchanged from `docs/HANDOFF.md` §8: another agent can read the repository and consistently produce a structured itinerary plus Markdown/HTML output without hidden context from this conversation, demonstrating clear boundaries, coherent route logic, explicit assumptions, explained exclusions, separation between canonical data and rendering, and a repeatable revision/validation loop.
