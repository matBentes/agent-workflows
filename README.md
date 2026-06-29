# Agent Workflows

Reusable agent workflow assets for Claude Code and OpenCode.

## Contents

- `skills/thermo-nuclear-code-quality-review/` - strict maintainability/code-quality review skill.
- `claude/commands/dual-opus.md` - Claude Code command for Opus-led OpenSpec review workflow.
- `opencode/commands/dual-gpt.md` - OpenCode command for GPT-5.5 implementation/fix workflow.
- `docs/dual-agent-workflow.md` - documented two-stage local + PR workflow.

## Install

From a project root:

```bash
mkdir -p .claude/commands .opencode/commands
cp /path/to/agent-workflows/claude/commands/dual-opus.md .claude/commands/dual-opus.md
cp /path/to/agent-workflows/opencode/commands/dual-gpt.md .opencode/commands/dual-gpt.md
```

Global skill install:

```bash
mkdir -p ~/.claude/skills
cp -r /path/to/agent-workflows/skills/thermo-nuclear-code-quality-review ~/.claude/skills/
```

Restart Claude Code and OpenCode after installing commands or skills.

## OpenSpec

Install OpenSpec separately:

```bash
npm install -g @fission-ai/openspec@latest
openspec init --tools claude,opencode
```

Then use the dual workflow documented in `docs/dual-agent-workflow.md`.
