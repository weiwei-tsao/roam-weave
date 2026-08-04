# travel-planner-milestone-1-polish Context

## Goal

Two independent small improvements to the travel-planner skill land as
separate, reviewed changes: (1) the Discover-flow research process can
optionally draw on Xiaohongshu content when the user's environment already
supports it, and (2) Claude Code can actually find/auto-trigger the
`travel-planner` skill, which it currently cannot.

## User-visible problem / business need

1. Destination research currently under-covers subjective, time-sensitive
   material (local/lesser-known spots, food recommendations, photo-op
   tips, current trends) that plain web search misses but Xiaohongshu
   content often has.
2. The milestone-1 post-merge live test confirmed `skills/travel-planner/`
   at the repo root is never auto-discovered/auto-triggered by Claude Code
   from a natural-language prompt — it only works when explicitly pointed
   at.

## Expected behavior

1. The Discover flow's research reference documents when/how Xiaohongshu
   content may be used as a supplementary source, without adding any
   dependency, login handling, or scraping code.
2. `travel-planner` is discoverable the way other Claude Code skills in
   this environment are (a `.claude/skills/`-style path), without
   duplicating skill content.

## Current behavior

Before this ticket: `skills/travel-planner/references/destination-research.md`
had no mention of social-media sourcing at all, and `skills/travel-planner/`
existed only at the repo root with no `.claude/skills/` counterpart.

## Scope

### In scope

- `skills/travel-planner/references/destination-research.md`: one new
  documentation-only subsection on optional Xiaohongshu sourcing.
- `.claude/skills/travel-planner`: a symlink to `../../skills/travel-planner`
  for discovery, plus the `workspace/ecosystem.md` doc update that explains it.

### Out of scope

- English-language social platforms (Reddit, etc.) — not decided yet,
  deferred to a future ticket.
- Any MCP tool integration, login/session/cookie handling, or scraping
  code for Xiaohongshu or any other platform.
- Collapsing the `skills/` ("product concept dir") vs `.claude/` ("tooling
  config dir") distinction — the symlink preserves it.
- Any change to `SKILL.md`, other `references/*.md`, or `templates/*`.

## Repos / modules likely involved

| Area | Repo / module | Why it may be involved |
|---|---|---|
| Skill logic | `skills/travel-planner/references/destination-research.md` | Task 1's target file |
| Discovery path | `.claude/skills/` (new) | Task 2's target — symlink to `skills/travel-planner/` |
| Repo map docs | `workspace/ecosystem.md` | Needs a line documenting the new symlink |

## Links

- Ticket: `workspace/tickets/travel-planner-milestone-1-polish/`
- PR: not opened yet
- Staging URL: n/a — no staging environment (`workspace/conventions.md` §Stages)
- Production URL: n/a
- Discussion thread: n/a — solo project, no external channel
- Design/spec: `docs/superpowers/specs/2026-08-02-social-media-sourcing-design.md`, `docs/superpowers/specs/2026-08-02-claude-skills-discovery-design.md`

## Important constraints

- Do not edit code until ownership is confirmed.
- Keep the diff minimal.
- Avoid unrelated formatting.

## Short title

Refine/optimize travel-planner skill content
