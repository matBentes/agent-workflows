# Example Dual OpenSpec Workflow

This example uses a feature request: add dark mode with system preference and a manual toggle.

Expected change name:

```text
add-dark-mode
```

## Local OpenSpec Workflow

Run in Claude Code:

```text
/dual-opus explore dark mode with system preference and manual toggle
```

Run in Claude Code:

```text
/dual-opus propose add dark mode with system preference and manual toggle
```

Run in OpenCode:

```text
/dual-gpt review-plan add-dark-mode
```

During plan review, install/use `grill-with-docs` when available:

```bash
npx skills add https://github.com/mattpocock/skills --skill grill-with-docs
```

Use it to challenge assumptions, missing docs, unclear requirements, and plan/spec gaps before approval.

Run in Claude Code:

```text
/dual-opus confirm-plan add-dark-mode
```

Run in OpenCode:

```text
/dual-gpt apply add-dark-mode
```

Run in Claude Code:

```text
/dual-opus review-impl add-dark-mode
```

If Opus requests fixes, run in OpenCode:

```text
/dual-gpt fix add-dark-mode
```

Then run final local approval in Claude Code:

```text
/dual-opus final-review add-dark-mode
```

Do not sync or archive yet if this work will go through a PR.

## PR Workflow

After opening the PR:

```text
Codex: code-review on PR
Sonar: PR annotations, warnings, and quality gate
```

If Codex, Sonar, or PR annotations have accepted findings, run in OpenCode:

```text
/dual-gpt pr-fix add-dark-mode
```

Confirm in Claude Code:

```text
/dual-opus final-review add-dark-mode
```

If CI fails, run in OpenCode:

```text
/dual-gpt ci-fix add-dark-mode
```

Confirm again in Claude Code:

```text
/dual-opus final-review add-dark-mode
```

After Codex review, Sonar, CI, and Opus final approval are complete, run in Claude Code:

```text
/dual-opus sync add-dark-mode
/dual-opus archive add-dark-mode
```

## View

To inspect OpenSpec changes visually:

```bash
openspec view
```

Useful terminal checks:

```bash
openspec list
openspec status --change add-dark-mode
openspec show add-dark-mode
```

## Review Skill

The Opus review phases use the global `thermo-nuclear-code-quality-review` skill:

- `/dual-opus review-impl add-dark-mode`
- `/dual-opus final-review add-dark-mode`

That skill is the strict code-quality gate for maintainability, structural simplicity, spaghetti growth, file-size growth, boundary leaks, and missed code-judo simplifications.

## Review Criteria

Implementation review, final review, PR fixes, and CI fixes should also check:

1. Alignment with approved plan/spec, task scope, non-goals, and accepted decisions.
2. Behavioral correctness for user-visible behavior, API contracts, persistence, and error handling.
3. Simplicity, readability, DRY/cohesion, useful SOLID boundaries, and minimal dependencies.
4. Safety for invalid input, security-sensitive behavior, data/schema changes, and migrations.
5. Verification evidence for relevant tests, formatters, linters, smoke checks, and skipped checks.
6. Technical debt: touched code should be at least as maintainable as before.
