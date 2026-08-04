# Session Handoff — roam-weave

No active ticket. `travel-planner-milestone-1-polish` closed and merged —
see `workspace/tickets/travel-planner-milestone-1-polish/handoff.md` for
its full history.

## Status

Repo is clean on `main`, nothing in flight. Last ticket
(`travel-planner-milestone-1-polish`) shipped as PR #3
(`3a13df5`, squash-merged), followed by a closeout commit (`d4f0cd7`). Its
feature branch was deleted, both locally and on the remote.

## Repo state (anchors)

- Branch: `main`, clean, at `d4f0cd7`.
- No open branches, no open PRs.

## Decisions carried forward (slow-decay)

- **Epistemic rigor pattern**: this user consistently pushes back when a
  causal claim outruns its evidence — don't assert "X confirms Y" from a
  single test/condition; treat "discovered" and "actually triggered" as
  separately-verified claims; verify config effects empirically (e.g. git
  index mode) rather than citing theoretical defaults. Apply proactively.
- User does their own `git commit`s — stage and stop.

## Next steps

None queued. When new work starts, open a new ticket under
`workspace/tickets/<slug>/` rather than reopening a closed one.

## Do-not-do / dead-ends

- Don't reopen `workspace/tickets/travel-planner-milestone-1/` or
  `workspace/tickets/travel-planner-milestone-1-polish/` — both closed.
- Don't rename `.handoff/HANDOFF.md` or `workspace/tickets/<slug>/handoff.md`
  — fixed paths this skill suite expects.
- A `/code-review` sub-agent once hallucinated a branch/ticket-slug
  mismatch during milestone-1 (claimed branch was
  `feat/travel-planner-phase-2`) — verified false at the time. Re-verify
  any such claim directly rather than trusting it, if it resurfaces.
