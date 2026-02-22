# OpenCode Config Repo Instructions

This repository is the shared agent configuration source for two runtimes:

- OpenCode (canonical source): `agents/` and `skills/`
- Codex compatibility layer: `AGENTS.md` and `.agents/skills/`

## Source of truth

- Treat `agents/*.md` as canonical for OpenCode agents.
- Treat `skills/*/SKILL.md` as canonical for reusable skills.
- Keep Codex compatibility wrappers in `.agents/skills/*` aligned with canonical files.

## Codex skill compatibility rules

- Codex discovers repository skills from `.agents/skills`.
- Skill files must include frontmatter with both `name` and `description`.
- For OpenCode agent reuse, prefer symlink wrappers:
  - `.agents/skills/<skill-name>/SKILL.md` -> `agents/<agent>.md`
- If a source file cannot be used directly, create a thin wrapper SKILL file that references the canonical source.

## Editing guardrails

- Do not edit `node_modules/`.
- Keep changes minimal and focused on config/instructions.
- Preserve OpenCode behavior first; Codex compatibility should be additive.

## Completion evidence rule

- Do not claim tasks are complete/fixed/passing without fresh verification evidence from this turn.
- Under fatigue or sunk-cost pressure, default to fail-closed wording: "Unverified" until command output is present.
- Completion claims must include concrete evidence: verification command, exit status, and key pass/fail signal.

## Cloud-task note

- Codex cloud tasks only load files present in the task repository/environment.
- If these instructions/skills are needed in another repository, copy or symlink them into that repository during setup.
