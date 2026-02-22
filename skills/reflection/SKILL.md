---
name: reflection
description: Use when a task has just finished and reflection is requested, especially after errors, repeated mistakes, rework, or confusion that should be prevented by updating AGENTS.md, skills, or agent definitions
---

# Reflecting on Completions

## Overview

Use reflection to improve the system, not just the message. A good reflection identifies what failed, why it failed, and what artifact to update so the same issue is less likely next time.

Core principle: if a mistake can recur, encode the fix in agent artifacts.

## When to Use

- Human asks to "reflect," "retro," "what to improve," or "what went wrong"
- You notice repeated mistakes, rework, confusing instructions, or avoidable delays
- You violated a process rule (for example: missing verification evidence)

Do not use for one-off environment outages outside agent control.

## Quick Reference

| Problem found | Update target |
|---|---|
| Instruction ambiguity or missing guardrail | `AGENTS.md` |
| Repeated execution/process mistake | `skills/<skill>/SKILL.md` |
| Agent role confusion or bad handoff | `agents/*.md` |

## Reflection Output Contract

Return these four parts:

1. `What happened`: one concrete mistake or inefficiency
2. `Root cause`: process gap, not just "I was rushed"
3. `Prevention`: exact guardrail/checklist step
4. `Artifact update plan`: filename + intended edit

If edits were made, cite the updated files. If not made, provide an exact patch plan.

## Implementation Pattern

```text
Reflection:
- What happened: Claimed completion before running final verification.
- Root cause: No mandatory completion gate under time pressure.
- Prevention: Add fail-closed completion rule requiring command evidence.
- Artifact update plan:
  - skills/verification-before-completion/SKILL.md: add sunk-cost red flag + response contract
  - agents/orchestrator.md: block "done" state when verification evidence is missing
```

## Common Rationalizations

| Excuse | Reality |
|---|---|
| "This was a one-off" | Repeated once is enough to add a guardrail |
| "No docs changes, move fast" | Tiny artifact updates are faster than repeated rework |
| "I will remember next time" | Memory is unreliable under pressure |
| "Reflection is just a paragraph" | Reflection without artifact change does not prevent recurrence |

## Red Flags - STOP and Harden

- You cannot name a specific root cause
- You propose "be more careful" without a concrete guardrail
- You skip artifact updates despite repeat failures
- You end reflection without filenames and edit intent

All of these mean: add a concrete artifact update before closing.
