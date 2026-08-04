# .claude/skills/ Discovery Symlink — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create `.claude/skills/travel-planner` as a symlink to `../../skills/travel-planner`, document it in `workspace/ecosystem.md`, empirically verify the symlink is real (both on disk and in git's index), and record the still-unverifiable discovery/activation live tests as an explicit follow-up.

**Architecture:** One new symlink, one small documentation edit, one small ticket-handoff edit. No code, no schema change, no change to any skill content file.

**Tech Stack:** Shell (`ln -s`), Markdown, git.

## Global Constraints

(from `docs/superpowers/specs/2026-08-02-claude-skills-discovery-design.md`)

- No change to any skill content (`SKILL.md`, `references/*.md`, `templates/*`).
- No collapsing of the `skills/` vs `.claude/` directory distinction — `skills/travel-planner/` stays the one canonical copy; `.claude/skills/travel-planner` has no content of its own.
- No cross-platform (Windows) symlink fallback — out of scope.
- The symlink must be verified empirically, not assumed correct from `core.symlinks` theory: git's index must record file mode `120000` for `.claude/skills/travel-planner` (checked via `git ls-files -s`).
- Discovery ("is the skill loaded at session start") and activation ("does a natural-language prompt actually invoke it") are two separate, currently-unverifiable-in-this-session questions — this task does NOT attempt to verify either. It only records them as an explicit follow-up for the user's next session.
- Repo convention: stage changes with `git add`, never run `git commit` — the user commits their own work.

---

### Task 1: Create the discovery symlink, document it, verify empirically, record the live-test follow-up

**Files:**
- Create (symlink): `.claude/skills/travel-planner` → `../../skills/travel-planner`
- Modify: `workspace/ecosystem.md:12-33` (repo-layout diagram)
- Modify: `workspace/tickets/travel-planner-milestone-1-polish/handoff.md:38-40` (Next steps section)

**Interfaces:**
- Consumes: nothing from other tasks — this is the only task in this plan.
- Produces: nothing consumed elsewhere — this plan has one task.

- [ ] **Step 1: Create the symlink**

```bash
mkdir -p .claude/skills
ln -s ../../skills/travel-planner .claude/skills/travel-planner
```

- [ ] **Step 2: Verify the symlink resolves correctly (static check, part 1)**

Run: `ls -la .claude/skills/travel-planner`

Expected: output ends with `travel-planner -> ../../skills/travel-planner` (confirms it's a symlink pointing at the right relative target).

Run: `diff .claude/skills/travel-planner/SKILL.md skills/travel-planner/SKILL.md`

Expected: no output (empty diff — both paths resolve to the exact same file content, since one is a symlink to the other's parent).

- [ ] **Step 3: Stage the symlink and verify git records it as a real symlink, not a dereferenced regular file (static check, part 2 — the empirical check that supersedes any `core.symlinks` config assumption)**

```bash
git add .claude/skills/travel-planner
git ls-files -s .claude/skills/travel-planner
```

Expected: the output's first field is `120000` (git's symlink mode). If it instead shows `100644` or `100755`, STOP and report BLOCKED — this means git dereferenced the symlink into a regular file (typically a `core.symlinks=false` effect either in this repo's environment or a global/system config override), and the fix as designed did not take effect; do not proceed to Step 4 in that case, since the doc update would then describe something that isn't actually true on disk.

- [ ] **Step 4: Update `workspace/ecosystem.md`'s repo-layout diagram**

Using the Edit tool, replace:

`````
```text
roam-weave/
├── README.md
├── README.zh-CN.md
├── docs/                                  # product decisions, handoff, findings
│   ├── README.md
│   ├── INITIAL_FINDINGS.md
│   ├── HANDOFF.md
│   ├── itinerary-data-model.md            # canonical entity definitions
│   └── superpowers/specs/                 # dated design specs (brainstorming skill output)
├── skills/
│   └── travel-planner/                    # the MVP skill
│       ├── SKILL.md
│       ├── references/                    # destination-research, route-planning,
│       │                                  # one-day-trip, multi-day-trip, quality-check
│       └── templates/                     # destination-brief.md, itinerary.md, itinerary.html
├── schemas/                               # canonical data contracts (JSON Schema)
│   ├── itinerary.schema.json
│   └── traveler-profile.schema.json
└── tests/
    └── fixtures/                          # oxford-one-day/, edinburgh-family-3-day/
```
`````

with:

`````
```text
roam-weave/
├── .claude/
│   └── skills/
│       └── travel-planner/                # symlink → ../../skills/travel-planner/ (Claude Code discovery path only, no content of its own)
├── README.md
├── README.zh-CN.md
├── docs/                                  # product decisions, handoff, findings
│   ├── README.md
│   ├── INITIAL_FINDINGS.md
│   ├── HANDOFF.md
│   ├── itinerary-data-model.md            # canonical entity definitions
│   └── superpowers/specs/                 # dated design specs (brainstorming skill output)
├── skills/
│   └── travel-planner/                    # the MVP skill (canonical source)
│       ├── SKILL.md
│       ├── references/                    # destination-research, route-planning,
│       │                                  # one-day-trip, multi-day-trip, quality-check
│       └── templates/                     # destination-brief.md, itinerary.md, itinerary.html
├── schemas/                               # canonical data contracts (JSON Schema)
│   ├── itinerary.schema.json
│   └── traveler-profile.schema.json
└── tests/
    └── fixtures/                          # oxford-one-day/, edinburgh-family-3-day/
```
`````

- [ ] **Step 5: Record the discovery/activation live-test follow-up in the ticket handoff**

Using the Edit tool, replace this exact block in
`workspace/tickets/travel-planner-milestone-1-polish/handoff.md`:

```
## Next steps

1. 
```

with:

```
## Next steps

1. **Manual, next session only** — verify discovery: start a *fresh*
   Claude Code session in this repo and check whether `travel-planner`
   appears in that session's available-skills listing (the system-reminder
   block enumerating skills with descriptions, shown at session start).
   Cannot be checked in the session that creates the symlink — skill
   listings are built at session start, before the change exists.
2. **Manual, next session only, after step 1 passes** — verify activation:
   in that same fresh session, give a natural-language prompt that should
   trigger travel-planner without naming it (e.g. "研究一下东京有什么好玩的"
   / "what's worth seeing in Tokyo"), and confirm the skill actually fired
   (an explicit invocation visible in the transcript) — not just that the
   reply reads like a travel plan, since the underlying model can produce
   a plausible-looking answer without the skill ever being invoked.
3. If step 1 passes but step 2 doesn't, the next debugging step is
   `skills/travel-planner/SKILL.md`'s `description` frontmatter (a
   matching problem), not the symlink — re-verify `git ls-files -s
   .claude/skills/travel-planner` still shows `120000` first, to rule out
   symlink regression before looking anywhere else.
```

- [ ] **Step 6: Stage the documentation changes and confirm final state**

```bash
git add workspace/ecosystem.md workspace/tickets/travel-planner-milestone-1-polish/handoff.md
git status
```

Expected: `.claude/skills/travel-planner` (new symlink), `workspace/ecosystem.md`, and `workspace/tickets/travel-planner-milestone-1-polish/handoff.md` all show as staged changes. No other files touched. Do **not** run `git commit` — this repo's convention is that the user commits their own work.
