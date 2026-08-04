# travel-planner-milestone-1-polish Investigation

## Reproduction steps

Not a bug ticket — two feature/polish items, each brainstormed and
designed before implementation (`superpowers:brainstorming` →
`superpowers:writing-plans` → `superpowers:subagent-driven-development`).
No reproduction steps apply; the "investigation" here is the design
research each brainstorming session did before writing its spec.

## Facts

- `skills/travel-planner/references/destination-research.md` had no
  mention of social-media sourcing before this ticket (confirmed by
  reading the file directly).
- `docs/HANDOFF.md` non-goals list (line ~483-484) states "Do not add APIs
  or scraping integrations yet" and "Keep planning logic source-agnostic"
  — a real prior decision that bounded Task 1's design.
- Open-source MCP servers for Xiaohongshu content do exist (e.g.
  `xiaohongshu-mcp` by xpzouying, 14.7k GitHub stars) but require a real
  Xiaohongshu account login/session to work around the platform's
  anti-bot measures — confirmed via web search during Task 1's
  brainstorming.
- `.claude/skills/travel-planner` did not exist before this ticket;
  `.claude/` only contained `settings.local.json` (confirmed via `ls -la
  .claude/`).
- `workspace/ecosystem.md`'s ownership table already designates
  `skills/travel-planner/` as owning "Planning workflow, rules,
  references, templates" — that ownership is unaffected by adding a
  discovery symlink.
- `core.symlinks` is unset at system, global, and local git config scope
  on this machine (checked directly with `git config --get core.symlinks`
  at each scope, all empty/exit 1) — its practical effect wasn't settled
  by config alone, which is why Task 2 verified the symlink's git index
  mode empirically instead of trusting the config default.

## Hypotheses

- Adding `.claude/skills/travel-planner` as a discovery path fixes
  auto-discovery/auto-trigger. This is the best available prior (every
  other skill visible in this project's own tooling resolves through a
  `.claude/skills/`-style path) but was **not** proven before
  implementation — see `docs/superpowers/specs/2026-08-02-claude-skills-discovery-design.md`
  §1 for the full reasoning and the alternative explanations that weren't
  ruled out (skill-scanning config, frontmatter/description matching,
  Claude Code version behavior, session working directory, discovered-but-
  not-triggered).
- Mid-implementation, this session's own system-reminder began listing
  `travel-planner` as available immediately after the symlink was
  created, without a session restart — suggestive that the hypothesis is
  correct, but it's one observation in one session, not a controlled test,
  and doesn't touch the separate activation question at all. See
  `handoff.md` Next steps.

## Decisions

- Xiaohongshu sourcing: documentation-only guidance, no MCP tool named, no
  login handling — `docs/superpowers/specs/2026-08-02-social-media-sourcing-design.md`
  §2.
- Discovery fix: symlink, not copy or directory move —
  `docs/superpowers/specs/2026-08-02-claude-skills-discovery-design.md`
  §2.1.
- Both live-test verifications (Xiaohongshu sourcing quality in practice;
  discovery/activation) are deferred to manual next-session checks, not
  attempted inside the implementing session — recorded in `handoff.md`.

## Data / component flow

```text
Entry (user asks to research/plan a trip)
→ SKILL.md routes to references/destination-research.md (Discover flow)
→ [Task 1] optionally supplemented by Xiaohongshu content, if an MCP tool is available
→ [Task 2] Claude Code must first discover the skill via .claude/skills/travel-planner
   to reach this flow at all from a natural-language prompt
```

## Files inspected

| Repo | File | Why inspected | Finding |
|---|---|---|---|
| roam-weave | `skills/travel-planner/references/destination-research.md` | Task 1 target | No existing social-sourcing guidance; dynamic-fact confidence rule at (then) lines 36-40 |
| roam-weave | `docs/HANDOFF.md` | Check non-goals before designing Task 1 | Confirmed "no APIs/scraping yet" and "source-agnostic" non-goals bind the design |
| roam-weave | `.claude/settings.local.json` | Check what `.claude/` currently contains | Only harness permissions, no `skills/` subdirectory |
| roam-weave | `workspace/ecosystem.md` | Check repo-layout doc and ownership table | `.claude/skills/travel-planner` needed a new diagram line; ownership table unaffected |
| roam-weave | `.handoff/HANDOFF.md`, `workspace/tickets/travel-planner-milestone-1/handoff.md` | Prior session's stated next-step candidates | Both confirmed the discovery gap as real (live-tested) but flagged the fix direction as untested |

## Root cause

Task 1: not a root-cause ticket — an additive capability gap (missing
sourcing guidance), not a defect.

Task 2: `skills/travel-planner/` living only at the repo root, with no
entry under `.claude/skills/` — the path Claude Code appears to scan for
project-level skills (inferred, not proven; see Hypotheses).

## Owner repo / module

Single repo (roam-weave). `skills/travel-planner/` owns skill logic/content
(unchanged by Task 2). `.claude/` is harness-level tooling config — Task 2's
symlink lives there but has no content of its own.

## Checked but not responsible

- `SKILL.md` frontmatter — read and confirmed already well-formed
  (`name`, `description` present); the discovery problem was never the
  file's content, only its filesystem location.

## Evidence

- `git ls-files -s .claude/skills/travel-planner` → `120000 ...` (symlink
  mode confirmed in git's index, not just on disk).
- Task reviewer reports for both tasks (spec ✅ compliant, 0 findings,
  Approved) — not re-transcribed here; see each task's `superpowers:subagent-driven-development`
  review in this session's history and the commits `6802334`, `0ca3065`.

## Open questions

- Does `travel-planner` actually get discovered in a genuinely fresh
  session (not just the implementing session)? Open — see `handoff.md`
  Next steps.
- Does a natural-language prompt actually activate `travel-planner` once
  discovered? Open — separate question, not yet tested at all.

## Next investigation steps

- Run the two-tier live test (discovery, then activation) in a fresh
  session per `handoff.md` Next steps 1-3.
