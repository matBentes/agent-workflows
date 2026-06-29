---
name: "Dual Agent: Opus"
description: Coordinate Claude Code Opus phases in the dual OpenSpec workflow
category: Workflow
tags: [workflow, openspec, review]
---

Run the Claude Code / Opus side of the dual-agent OpenSpec workflow.

Input format:

```text
/dual-opus <phase> <change-or-request>
```

Phases:

- `explore`: investigate an idea before creating OpenSpec artifacts. Mirrors `/opsx:explore`.
- `propose`: create OpenSpec planning artifacts. Mirrors `/opsx:propose`.
- `confirm-plan`: review GPT-5.5's plan review and update artifacts only for accepted feedback.
- `review-impl`: review GPT-5.5's applied implementation before final approval.
- `final-review`: final Opus approval after fixes, tests, PR review, or CI evidence.
- `sync`: sync delta specs after PR review, CI, and final approval. Mirrors `/opsx:sync`.
- `archive`: archive the completed OpenSpec change. Mirrors `/opsx:archive`.

Rules:

- Use OpenSpec artifacts as the source of truth.
- Use `openspec status --change <change> --json` before reading or writing phase artifacts.
- Put handoff files in `openspec/changes/<change>/reviews/`.
- Do not edit implementation files during review phases unless correcting OpenSpec artifacts.
- Run relevant local checks before claiming approval.
- Use the Review Criteria below during implementation review and final review.

Review Criteria:

- Plan/spec alignment: implementation matches approved scope, non-goals, and accepted decisions without speculative behavior.
- Behavioral correctness: user-visible behavior, API contracts, persistence, and error handling match requirements and existing architecture.
- Simplicity, readability, DRY/cohesion, useful SOLID boundaries, and minimal dependencies.
- Safety: invalid input fails fast; security-sensitive behavior, data/schema changes, and migrations are safe and explicitly approved.
- Tests and verification: relevant checks and skipped-check reasons are recorded with evidence.
- Technical debt: touched code stays at least as maintainable as before.

Phase behavior:

## explore

Investigate the topic, read relevant code, compare options, and clarify requirements. Do not create OpenSpec artifacts or write implementation code.

End with a recommended change name and next command:

```text
/dual-opus propose <change-or-request>
```

## propose

Derive a kebab-case change name if needed, then create the OpenSpec planning artifacts using OpenSpec CLI instructions:

```bash
openspec new change "<change>"
openspec status --change "<change>" --json
openspec instructions proposal --change "<change>" --json
openspec instructions specs --change "<change>" --json
openspec instructions design --change "<change>" --json
openspec instructions tasks --change "<change>" --json
```

Create the required artifacts. When done, tell the user:

```text
/dual-gpt review-plan <change>
```

## confirm-plan

Read the OpenSpec artifacts and `reviews/plan-gpt-5.5.md`. Accept only technically valid feedback and update artifacts for accepted changes.

Write `openspec/changes/<change>/reviews/plan-opus-confirmation.md` with accepted items, rejected items, risks, and next command:

```text
/dual-gpt apply <change>
```

## review-impl

Read the OpenSpec artifacts, `reviews/plan-opus-confirmation.md`, `reviews/implementation-gpt-5.5-summary.md`, and the current diff.

Use the global `thermo-nuclear-code-quality-review` skill for this review. Apply its strict maintainability bar plus the Review Criteria above: look for structural regressions, spaghetti branching, unnecessary abstractions, file-size growth, boundary leaks, missed code-judo simplifications, and plan/spec drift.

Write `openspec/changes/<change>/reviews/implementation-opus-review.md` with one verdict:

- `approved`
- `needs-gpt-fix`
- `needs-human-decision`

If fixes are needed, tell the user:

```text
/dual-gpt fix <change>
```

If no fixes are needed, tell the user:

```text
/dual-opus final-review <change>
```

## final-review

Review OpenSpec artifacts, review files, current diff, tests, PR feedback, Sonar feedback, and CI evidence.

Use the global `thermo-nuclear-code-quality-review` skill and the Review Criteria above when evaluating code quality and maintainability. Do not approve merely because tests pass if the implementation creates avoidable structural debt or drifts from the approved plan/spec.

Write `openspec/changes/<change>/reviews/final-opus-confirmation.md` with one verdict:

- `approved`
- `needs-gpt-fix`
- `needs-human-decision`

If approved and PR review/CI are complete, tell the user:

```text
/dual-opus sync <change>
```

## sync

Use `/opsx:sync` behavior. Only run after Codex PR review, accepted fixes, CI, and Opus final confirmation are complete.

## archive

Use `/opsx:archive` behavior. Validate when feasible, then archive the completed change and preserve review files in the archive.
