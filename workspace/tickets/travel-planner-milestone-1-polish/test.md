# travel-planner-milestone-1-polish Test Plan

## Local checks

- [x] `git status` checked before testing — clean at start of each task
- [x] Typecheck — n/a, no code (Markdown/symlink only)
- [x] Unit tests, if relevant — n/a, no code; verification was grep/diff/`git ls-files -s` per each plan's static checks, both confirmed by an independent task reviewer (not just the implementer's own claim)
- [x] Lint, if relevant — n/a, no linter configured in this repo (checked: no `package.json`, no `.markdownlint*`, no CI workflow)
- [x] Affected page/component checked locally, if possible — read-through of both changed files confirmed by task review (heading hierarchy consistent, no contradiction with existing dynamic-fact rule)

## Environment checks

| Environment / surface | URL | Checked | Notes |
|---|---|---|---|
| Local repo (only environment — no staging/UAT per `conventions.md` §Stages) | n/a | [x] | `git ls-files -s .claude/skills/travel-planner` → `120000`; `destination-research.md` heading hierarchy confirmed consistent |

## Regression checks

- [x] Existing behavior still works — no skill content changed except the one new subsection; existing dynamic-fact rule (lines 36-40 pre-change) untouched and confirmed non-contradictory with the new text
- [x] No new console/log errors — n/a, no runtime code
- [x] No obvious visual regression — n/a, no UI
- [x] No unrelated area affected — both task reviewers confirmed only the files named in each brief were touched

## Acceptance

| Person | Role | Status | Date | Notes |
|---|---|---|---|---|
| weiwei cao | Solo owner/self-reviewer | Pending | | Self-review only per `conventions.md` §Stages — no external approver at this stage |

## Production deploy check

- [ ] Accepted on staging — n/a, no staging environment
- [ ] Safe for production — pending merge/PR decision
- [ ] Deployed — n/a, this repo has no deploy step (documentation/skill content)
- [ ] Ticket marked done — pending the two live-test follow-ups below
- [ ] Status update sent, if needed — n/a, solo project, no external channel

## Final test notes for PR

```text
- Both tasks verified via independent task-reviewer subagents (spec ✅
  compliant, 0 findings, Approved) under superpowers:subagent-driven-development,
  not just implementer self-report.
- Task 2's fix is NOT fully verified yet: discovery/activation require a
  fresh Claude Code session to test live (see
  workspace/tickets/travel-planner-milestone-1-polish/handoff.md Next
  steps 1-3). A mid-session observation during implementation
  (travel-planner appeared in this session's own skill listing right
  after the symlink was created) is suggestive but not a substitute for
  that fresh-session check.
```
