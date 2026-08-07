# Session Handoff — roam-weave

Active ticket: `travel-planner-planning-correctness` — Phase 3 done,
waiting on the user's own `git commit`. Full detail:
`workspace/tickets/travel-planner-planning-correctness/handoff.md`.

Previous ticket `travel-planner-milestone-1-polish` closed and merged —
see `workspace/tickets/travel-planner-milestone-1-polish/handoff.md`.

## Status

Repo on `main`, 1 file modified + 1 untracked ticket dir, not yet
committed:

- `skills/travel-planner/references/quality-check.md` — 2 new checklist
  items (landside/airside state consistency; Assumption-vs-timeline
  consistency), fixing 2 confirmed defects found by an external review of
  3 real-trip fixtures.
- `workspace/tickets/travel-planner-planning-correctness/` — ticket
  workspace, all phases filled.

## Repo state (anchors)

- Branch: `main`.
- `git status`: `skills/travel-planner/references/quality-check.md`
  modified (unstaged); `workspace/tickets/travel-planner-planning-correctness/`
  untracked.
- No open PR yet — user hasn't committed.

## Decisions carried forward (slow-decay)

- **Epistemic rigor pattern**: this user consistently pushes back when a
  causal claim outruns its evidence — don't assert "X confirms Y" from a
  single test/condition; verify claims (including from external
  reviews/reports) against actual files rather than trusting them.
  Reconfirmed this session: one review claim (output truncation) was
  checked and found false before being excluded from scope.
- User does their own `git commit`s — stage and stop.
- Prefer extending existing documentation-checklist mechanisms
  (`quality-check.md`) over introducing new schema fields or executable
  code, unless investigation shows the checklist genuinely can't carry
  the check.

## Next steps

1. User reviews and commits the `quality-check.md` diff (assistant
   stages only, doesn't commit in this repo).
2. If pursuing further: the external review
   (`/Users/bule-station/Downloads/roamweave_three_plan_review.md`) has
   several more claims not yet individually verified — see
   `workspace/tickets/travel-planner-planning-correctness/investigation.md`
   Open questions. Each needs the same line-level verification the first
   two got before becoming new ticket scope.

## Do-not-do / dead-ends

- Don't reopen `workspace/tickets/travel-planner-milestone-1/` or
  `workspace/tickets/travel-planner-milestone-1-polish/` — both closed.
- Don't rename `.handoff/HANDOFF.md` or `workspace/tickets/<slug>/handoff.md`
  — fixed paths this skill suite expects.
- Don't build an executable constraint-solver/state-machine/topology
  engine for `travel-planner` — explicitly rejected this session;
  `quality-check.md` is documentation-only by design. Would need fresh
  investigation + user sign-off to revisit.
- A `/code-review` sub-agent once hallucinated a branch/ticket-slug
  mismatch during milestone-1 (claimed branch was
  `feat/travel-planner-phase-2`) — verified false at the time. Re-verify
  any such claim directly rather than trusting it, if it resurfaces.
