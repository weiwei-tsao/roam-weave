# Xiaohongshu as an Optional Discovery Source (travel-planner-milestone-1-polish)

**Status:** Approved by user, pending write-up into implementation plan
**Date:** 2026-08-02
**Supersedes/refines:** `skills/travel-planner/references/destination-research.md` (adds a new subsection; does not replace the existing process)
**Ticket:** `workspace/tickets/travel-planner-milestone-1-polish/`

## 1. Context

The user wants the Discover flow's research step to be able to draw on
Xiaohongshu (小红书/RedNote) content — local/lesser-known spots, food
recommendations, photo-op tips, current trends — the kind of subjective,
time-sensitive material that plain web search under-covers.

Two mechanisms were considered and rejected during brainstorming:

- Have the Agent fetch Xiaohongshu content directly via `WebSearch`/`WebFetch`.
  Xiaohongshu enforces anti-bot measures that block this in practice.
- Build or adopt a scraping/API integration inside `travel-planner` itself
  (e.g. wrapping an open-source project like `xiaohongshu-mcp`). Rejected:
  every such tool requires a real Xiaohongshu account login (cookie/session)
  to work around the anti-bot layer, which carries an account-suspension
  risk the user is not willing to have `travel-planner` manage, and it would
  reverse the existing Milestone 1 decision to not add API/scraping
  integrations yet (`docs/HANDOFF.md` — non-goals list) and to keep planning
  logic source-agnostic.

The approach that survived: **documentation-only, pluggable guidance**.
`travel-planner` adds no dependency, no login handling, and no scraping code
of its own. If the user's own environment already has *some* MCP tool
capable of reading Xiaohongshu content configured (their choice, their
account, their risk), the Agent is told it may use it as a supplementary
source. If not, the step is skipped silently — current behavior is
unchanged.

English-language platform sourcing (Reddit, etc.) was explicitly scoped
**out** of this ticket — deferred to a future ticket once the user has
decided which platform(s) to target.

## 2. Design

### 2.1 Where the change lives

One file: `skills/travel-planner/references/destination-research.md`. No new
file, no schema change, no change to `SKILL.md`'s trigger/routing logic —
this is a small addition to the existing Discover-flow research process, not
a new capability surface.

### 2.2 New subsection: "Optional: Xiaohongshu as a supplementary source"

Added after the existing 4-step Process section. Content:

- **Availability check, not installation instruction.** If an MCP tool
  capable of searching/reading Xiaohongshu content is already available in
  the current environment, it may be used as a supplementary discovery
  source. The doc does not name a specific project — naming one bakes in a
  dependency on a specific external tool's continued existence/naming, and
  the user hasn't chosen one. If no such tool is available, skip this step;
  do not suggest installing one, do not attempt browser automation or login.
- **What it's for**: subjective/experiential material that plain web search
  under-covers — local/lesser-known candidates, food recommendations,
  photography angles, current popularity/trends. This is Discover-flow
  material (candidate discovery), not Plan-flow material.
- **What it is not for**: dynamic facts (`opening_hours`, `estimated_cost`,
  `reservation`) still follow the existing rule in this same file (lines
  36–40) — every dynamic fact needs a `Source` with `confidence`. A single
  Xiaohongshu note is explicitly called out as **not sufficient alone** to
  mark a dynamic fact `confidence: verified` — personal blogger content is
  treated as unverified by default; if it's the only evidence, the fact
  must be recorded `confidence: uncertain` and surfaced as such in the
  output, same as any other under-verified dynamic fact.

### 2.3 Non-goals (explicit, to prevent scope creep later)

- No specific MCP project named or recommended in skill docs.
- No login/session/cookie handling logic anywhere in `travel-planner`.
- No English-platform equivalent in this ticket.
- Does not reverse `docs/HANDOFF.md`'s "no APIs/scraping integrations yet"
  or "keep planning logic source-agnostic" — no integration or dependency
  is added; the addition is conditional guidance for an Agent operating in
  an environment the user has already configured themselves.

## 3. Testing

No code changes — this is a documentation-only change to an Agent-facing
reference file. Verification is a read-through: confirm the new subsection
doesn't contradict the existing dynamic-fact rules in the same file, and
(optionally) a live test prompting the Discover flow for a destination in an
environment both with and without a Xiaohongshu-capable MCP tool configured,
confirming the skip-silently path produces no visible difference in output
when the tool is absent.
