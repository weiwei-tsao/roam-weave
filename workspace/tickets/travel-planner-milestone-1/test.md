# travel-planner-milestone-1 Test Plan

## Local checks

- [x] `git status` checked before testing
- [ ] Typecheck — N/A, no application code (docs/JSON Schema/Markdown only)
- [x] Unit tests, if relevant — no test framework in this repo; validated schemas/fixtures directly with `python3`'s `jsonschema` library (installed ephemerally, not added as a repo dependency)
- [ ] Lint, if relevant — N/A, no linter configured
- [x] Affected page/component checked locally, if possible — every generated Markdown/HTML file read back and checked for internal consistency

## Environment checks

N/A — no deployed environment. This repo ships Markdown/JSON/JSON-Schema
content consumed directly by an agent invoking the `travel-planner`
skill, not a running application.

## Regression checks

- [x] Existing behavior still works — greenfield branch, nothing pre-existing to regress except `workspace/ecosystem.md` (doc-only edit, re-read after editing to confirm it stayed internally consistent).
- [ ] No new console/log errors — N/A
- [ ] No obvious visual regression — N/A (`itinerary.html` fixture is new, not a regression target)
- [x] No unrelated area affected — `/code-review` Standards axis confirmed no unrelated files were touched.

## Acceptance

| Person | Role | Status | Date | Notes |
|---|---|---|---|---|
| weiwei cao | Author/self-reviewer | Accepted | 2026-08-01 | Solo project, self-review only per `conventions.md` "Stages / acceptance" — no external approver at this stage. |

## Production deploy check

N/A — no staging/production environment for this repo
(`conventions.md` "Stages / acceptance": "No staging/UAT environment...
at this stage"). "Deployed" here means merged to `main`; see `pr.md`'s
"Status update — deployed".

## Final test notes for PR

```text
- Both schemas: valid Draft 2020-12; validated against hand-built
  valid/invalid instances (missing required field, extra property, bad
  enum) — all correctly accepted/rejected.
- Both fixtures' itinerary.json validate against schemas/itinerary.schema.json.
- Every Stop.candidate_id / CandidateStatus.candidate_id in both fixtures
  cross-checked against the destination-brief.md headings — full 1:1
  coverage, no orphans.
- edinburgh-family-3-day/itinerary.html checked for balanced/well-formed
  HTML tags via Python's html.parser.
- /code-review (Standards + Spec axes) run against main...HEAD: Spec — 0
  findings; Standards — 2 minor findings, both resolved or accepted (see
  pr.md).
```
