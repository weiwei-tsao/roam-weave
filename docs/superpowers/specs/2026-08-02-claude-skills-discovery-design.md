# Fix travel-planner Auto-Discovery via .claude/skills/ Symlink (travel-planner-milestone-1-polish)

**Status:** Approved by user, pending write-up into implementation plan
**Date:** 2026-08-02
**Supersedes/refines:** `workspace/ecosystem.md` (adds a repo-layout entry; does not change any skill content)
**Ticket:** `workspace/tickets/travel-planner-milestone-1-polish/`

## 1. Context

`skills/travel-planner/` lives at the repo root, not under `.claude/skills/`.
This was flagged as a real (not theoretical) gap during the milestone-1
post-merge live test: Claude Code did not auto-discover or auto-trigger the
skill from a natural-language prompt — it only got used when explicitly
pointed at (`.handoff/HANDOFF.md` line 42,
`workspace/tickets/travel-planner-milestone-1/handoff.md` lines 21–26).

That prior finding only confirms: in that one live test, with
`skills/travel-planner/` at the repo root and not under `.claude/skills/`,
the skill was not auto-discovered/auto-triggered. It does not isolate "not
being under `.claude/skills/`" as the cause — the test didn't rule out
other candidate explanations (skill-scanning directory configuration,
frontmatter/description matching, Claude Code version behavior, the
session's working directory at startup, or the skill being discovered but
not triggered by that particular prompt). Nor does it confirm that *adding*
the skill under `.claude/skills/` fixes anything — that direction has not
been tested at all.

This spec proceeds on the `.claude/skills/` hypothesis because it matches
how every other skill visible in this project's own tooling is structured
(plugin skills, `superpowers:*`, etc. all resolve through a
`.claude/skills/`-style path) — the best available prior, not a confirmed
diagnosis. If the live verification in §3 still fails to trigger the skill
after this change, the next step is to check the other candidate
explanations above, not to assume the symlink was built wrong.

## 2. Design

### 2.1 Mechanism: symlink, not copy, not move

`.claude/skills/travel-planner` is created as a symlink to
`../../skills/travel-planner`. Rejected alternatives:

- **Copy**: creates two physical copies of the same skill content that will
  drift out of sync the next time anyone edits `references/*.md` or
  `SKILL.md` and forgets the second location.
- **Move `skills/` entirely into `.claude/`**: collapses the repo's existing
  "product concept dir" (`skills/` — the shippable skill content,
  documented in `workspace/ecosystem.md`'s ownership table as owning
  "Planning workflow, rules, references, templates") vs. "tooling config
  dir" (`.claude/` — harness-level configuration, currently just
  `settings.local.json`) distinction. That distinction is intentional and
  predates this ticket; collapsing it is a much bigger, unrelated change
  this ticket doesn't need.

A symlink keeps exactly one copy of real content (`skills/travel-planner/`)
and makes `.claude/skills/travel-planner` a pure discovery-path pointer with
no content of its own. `core.symlinks` is unset at every config scope on
this machine (system, global, and local — checked directly, not assumed),
so its practical effect here isn't settled by config alone. What actually
matters isn't the config's theoretical default but whether the symlink
this task creates is (a) a real filesystem symlink and (b) recorded as one
in git's index (mode `120000`) — both checked empirically in §3, Task
implementation step, before anything downstream relies on it. This repo
has one solo macOS user and no cross-platform CI (per `workspace/ecosystem.md`),
so there's no other environment where a different config value could
silently change this behavior.

No change to `skills/travel-planner/SKILL.md` or any reference/template
file — the frontmatter (`name`, `description`) is already well-formed;
the problem was never the file's content, only its location relative to
where Claude Code looks.

### 2.2 Documentation update

`workspace/ecosystem.md`'s `## Repo layout` diagram gets a new line for
`.claude/skills/travel-planner` noting it's a symlink to `skills/travel-planner/`,
so a future session reading the repo map isn't confused by an
entry that resolves to content living elsewhere. The `## Repos and
ownership` table is unaffected — `skills/travel-planner/` remains the
owner of skill logic; the symlink doesn't create a second owner.

### 2.3 Non-goals

- No change to any skill content (`SKILL.md`, `references/*.md`,
  `templates/*`).
- No collapsing of the `skills/` vs `.claude/` directory distinction.
- No cross-platform (Windows) symlink fallback — out of scope; this repo
  has one solo macOS user and no CI that checks out the repo elsewhere.

## 3. Testing

This is a structural fix, not a content change — there is no unit test for
"does Claude Code's skill scanner pick this up." Discovery and activation
are two different questions and must be verified separately — fixing one
does not prove the other:

- **Discovery**: at session startup, does Claude Code load and recognize
  `travel-planner` as an available skill at all?
- **Activation**: given a natural-language prompt, does Claude Code
  actually decide to invoke `travel-planner` for it?

A skill can be discovered but never activated (its `description`
frontmatter doesn't match the prompt closely enough to win selection). And
a plausible-looking travel answer proves nothing about activation either —
the underlying model can produce a decent destination summary on its own,
with the skill never invoked. Confirming activation requires seeing that
the skill actually fired (e.g. an explicit "Using travel-planner…"
announcement or a visible skill-invocation entry in the transcript), not
just judging whether the output looks right.

Verification is three-tiered:

1. **Static check** (verifiable now, in this session): confirm the symlink
   resolves correctly — `ls -la .claude/skills/travel-planner` shows it
   pointing at `../../skills/travel-planner`, and `cat
   .claude/skills/travel-planner/SKILL.md` reads the same content as
   `skills/travel-planner/SKILL.md` (proves the symlink is mechanically
   correct). Additionally, confirm git actually records it as a symlink
   rather than silently dereferencing it into a regular file: after
   `git add .claude/skills/travel-planner`, `git ls-files -s
   .claude/skills/travel-planner` must show file mode `120000` (git's
   symlink mode), not `100644`/`100755` (regular file). This is the
   empirical check that supersedes any theoretical claim about
   `core.symlinks`'s default — if the mode comes back wrong, the fix
   didn't do what §2.1 assumes, regardless of what any config says.
2. **Live discovery check** (cannot be done in this session — skill
   listings are built at session start, before this change exists): start a
   **fresh** Claude Code session in this repo, and check whether
   `travel-planner` appears in that session's available-skills listing
   (the system-reminder block enumerating skills with their descriptions,
   visible at session start). Pass/fail is a plain lookup, not an inference
   from output.
3. **Live activation check** (same fresh session, after the discovery
   check passes): issue a natural-language prompt that should trigger
   travel-planner without naming it (e.g. "研究一下东京有什么好玩的" /
   "what's worth seeing in Tokyo"), and confirm the skill actually fired —
   an explicit invocation visible in the transcript — not merely that the
   reply resembles a travel plan. If discovery passes but activation still
   doesn't fire, the next step is to revisit `SKILL.md`'s `description`
   frontmatter for a matching problem, not to assume the symlink failed.

Both live checks are manual steps for the user to run in their next
session, not something this task's implementer can complete — record both
as explicit follow-ups in the task's handoff rather than silently assuming
success.
