# Engineering Conventions

Conventions AI should follow when generating code, PR notes, test
notes, and status updates for this repo.

## General working style

- Keep changes small and focused; avoid unrelated formatting.
- Avoid broad refactors in bug-fix tickets.
- Prefer evidence over assumptions; separate facts, hypotheses, decisions.
- Confirm which module owns a change (see `ecosystem.md`) before editing.
- Keep environment/acceptance status explicit.

## Ticket IDs

No formal tracker or ID scheme — solo project, early MVP stage.

Use a short kebab-case slug instead (e.g. `phase-1-schema`,
`itinerary-validation`). The slug doubles as the `tickets/<slug>/`
folder name. `./scripts/new-ticket.sh <slug> "Short title"` accepts
this slug in place of a `TICKET-ID`.

## Branch naming

Matches the pattern already in use on this repo (e.g.
`feat/travel-planner-phase-1`):

```text
feat/<slug>
fix/<slug>
chore/<slug>
docs/<slug>
```

## Commit / PR title style

Conventional Commits, no ticket reference (matches existing history —
`docs: add handoff doc`, `docs: organize MVP docs and add Chinese README`):

```text
<type>: short description
```

Types: `feat`, `fix`, `docs`, `chore`, `refactor`, `test`. Keep the
description under ~10 words when possible.

## PR description style

```md
## Summary
- ...

## Test
- ...

## Notes
- ...
```

Concise and evidence-based.

## Status updates

Solo project — no external channel. Use commit messages and
`tickets/<slug>/handoff.md` as the status record between sessions.

## Stages / acceptance

- Self-review only. No staging/UAT environment and no external
  approver at this stage.
- Record self-review completion (what was checked, when) in
  `timeline.md` and `test.md`.
- Before merging to `main`: re-read `docs/HANDOFF.md` §6.4 (MVP
  non-goals) to confirm scope wasn't quietly expanded.

## AI instructions

When asked to work on a ticket:

1. Read `ecosystem.md` and `conventions.md`.
2. Read the ticket's `context.md` and `handoff.md`.
3. If debugging, update `investigation.md` before code changes.
4. Before editing: current repo, branch, `git status`, current diff.
5. Make the smallest safe change.
6. Update `implementation.md`, `test.md`, `handoff.md`, and `pr.md`.
