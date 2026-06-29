---
description: Coordinate OpenCode GPT-5.5 phases in the dual OpenSpec workflow
---

Run the OpenCode / GPT-5.5 side of the dual-agent OpenSpec workflow.

Input format:

```text
/dual-gpt <phase> <change>
```

Phases:

- `review-plan`: review Opus-created OpenSpec artifacts.
- `apply`: implement the confirmed OpenSpec plan. Mirrors `/opsx:apply`.
- `fix`: apply fixes accepted by Opus.
- `pr-fix`: fix accepted Codex PR review findings, Sonar warnings, and non-failing annotations.
- `ci-fix`: fix failing checks, including failed Sonar quality gates.

Rules:

- Use OpenSpec artifacts as the source of truth.
- Use `openspec status --change <change> --json` before reading phase artifacts.
- Put handoff files in `openspec/changes/<change>/reviews/`.
- During `review-plan`, do not edit implementation files.
- During fix phases, change only what is needed for accepted findings or failures.

## review-plan

Read proposal, specs, design, tasks, and relevant project docs.

Write `openspec/changes/<change>/reviews/plan-gpt-5.5.md` with:

- verdict: `approve`, `approve-with-changes`, or `block`
- findings ordered by severity
- open questions
- next command: `/dual-opus confirm-plan <change>`

## apply

Read OpenSpec artifacts and `reviews/plan-opus-confirmation.md`, then query apply instructions:

```bash
openspec instructions apply --change "<change>" --json
```

Implement tasks, check off `tasks.md` only after code and verification are done, and write `reviews/implementation-gpt-5.5-summary.md`.

Tell the user:

```text
/dual-opus review-impl <change>
```

## fix

Read Opus review files and apply only accepted fixes. Run relevant checks and write `reviews/fixes-gpt-5.5.md`.

Tell the user:

```text
/dual-opus final-review <change>
```

## pr-fix

Inspect pasted PR feedback, `gh` if available, Codex review findings, Sonar warnings, GitHub review comments, and non-failing annotations.

Fix only technically valid findings accepted by the user or Opus. Write `reviews/pr-gpt-5.5.md`.

Tell the user:

```text
/dual-opus final-review <change>
```

## ci-fix

Fix only failing required checks: failed tests, builds, lint, failed Sonar quality gates, and failed CI jobs. Write `reviews/ci-gpt-5.5.md`.

Tell the user:

```text
/dual-opus final-review <change>
```
