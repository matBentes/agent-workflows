# Dual-Agent OpenSpec Workflow

Use this workflow when Claude Code and OpenCode should cooperate on the same OpenSpec change.

## Roles

- Claude Code / Opus owns exploration, proposal, plan confirmation, implementation review, final acceptance, sync, and archive.
- OpenCode / GPT-5.5 owns adversarial plan review, apply/implementation, targeted fixes, PR warning fixes, and CI failure fixes.
- OpenSpec owns shared state through `openspec/changes/<change>/`.
- Review handoffs live in `openspec/changes/<change>/reviews/`.

## Commands

Claude Code:

```text
/dual-opus explore <idea>
/dual-opus propose <change-or-request>
/dual-opus confirm-plan <change>
/dual-opus review-impl <change>
/dual-opus final-review <change>
/dual-opus sync <change>
/dual-opus archive <change>
```

OpenCode:

```text
/dual-gpt review-plan <change>
/dual-gpt apply <change>
/dual-gpt fix <change>
/dual-gpt pr-fix <change>
/dual-gpt ci-fix <change>
```

## OpenSpec Mapping

- `/dual-opus explore` maps to `/opsx:explore`.
- `/dual-opus propose` maps to `/opsx:propose`.
- `/dual-gpt apply` maps to `/opsx:apply`.
- `/dual-opus sync` maps to `/opsx:sync`.
- `/dual-opus archive` maps to `/opsx:archive`.
- Review and fix commands are custom gates inserted between OpenSpec actions.

## Local Workflow

```text
Claude Code: /dual-opus explore <idea>
Claude Code: /dual-opus propose <request>
OpenCode:    /dual-gpt review-plan <change>
Claude Code: /dual-opus confirm-plan <change>
OpenCode:    /dual-gpt apply <change>
Claude Code: /dual-opus review-impl <change>
OpenCode:    /dual-gpt fix <change>       # only if Opus requests fixes
Claude Code: /dual-opus final-review <change>
```

Do not run `sync` or `archive` yet if the work will go through a PR.

## Plan Review Skill

During plan review, install/use Matt Pocock's `grill-with-docs` skill when available:

```bash
npx skills add https://github.com/mattpocock/skills --skill grill-with-docs
```

Use it before plan approval to challenge assumptions, missing docs, unclear requirements, incomplete acceptance criteria, and plan/spec gaps.

## Review Criteria

Use these criteria for implementer self-review, independent implementation review, final review, PR warning fixes, and CI fixes:

1. Plan/spec alignment: the implementation satisfies the approved OpenSpec change, task scope, non-goals, and accepted review decisions without adding speculative behavior.
2. Behavioral correctness: user-visible behavior, API contracts, persistence behavior, and error handling match the documented requirements and existing architecture.
3. Simplicity: the solution is as small as practical, avoids unnecessary abstractions, and keeps the implementation understandable.
4. DRY and cohesion: duplicated logic is avoided where it would create maintenance risk, and each changed unit has a clear responsibility with low coupling.
5. SOLID where useful: composition is preferred over inheritance, boundaries are clear, and abstractions exist only when they improve maintainability.
6. Readability: code favors clear names and straightforward control flow over cleverness; comments explain non-obvious decisions only.
7. Safety: invalid input fails fast, security-sensitive behavior is preserved or explicitly approved, data/schema changes are safe, and existing migrations are not edited.
8. Minimal dependencies: new dependencies are justified by the plan and do not replace simple local code without clear benefit.
9. Tests and verification: relevant automated tests, formatters, linters, smoke checks, and skipped-check reasons are recorded with evidence.
10. Technical debt: the change leaves touched code at least as maintainable as before and does not hide known follow-up work.

## Review Skill

Claude Code review phases use the global `thermo-nuclear-code-quality-review` skill:

- `/dual-opus review-impl <change>` uses it for the first implementation review.
- `/dual-opus final-review <change>` uses it for final code-quality approval.

The skill is intentionally strict. It should block structural regressions, spaghetti branching, unnecessary abstractions, avoidable file-size growth, boundary leaks, and missed opportunities for simpler code-judo restructuring. Apply it together with the Review Criteria above.

## PR Workflow

```text
Open PR
Codex: code-review on PR
Sonar: PR annotations, warnings, and quality gate
OpenCode:    /dual-gpt pr-fix <change>    # accepted Codex/Sonar/PR annotation findings; must satisfy Review Criteria
Claude Code: /dual-opus final-review <change> # use Review Criteria + verification evidence
OpenCode:    /dual-gpt ci-fix <change>    # only if CI fails; stay within approved scope
Claude Code: /dual-opus final-review <change> # use Review Criteria + verification evidence
Claude Code: /dual-opus sync <change>
Claude Code: /dual-opus archive <change>
Merge PR
```

## Boundary Rule

Only run `sync` and `archive` after Codex PR review, accepted fixes, CI, and Opus final confirmation are complete.

## View

To inspect OpenSpec changes visually, run this in a terminal from the repo root:

```bash
openspec view
```

Useful inspection commands:

```bash
openspec list
openspec status --change <change>
openspec show <change>
```
