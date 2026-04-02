---
name: manual-review-response
description: Use when human review comments are embedded in source as REVIEW: markers and each marker must be resolved by implementing a fix or providing an evidence-based defense, then removed with a per-comment response report
---

# Manual Review Response

## Overview

When a human leaves inline `REVIEW:` markers, treat each marker as a required review item.

Every marker must end in one of two states:
- code changed to address the critique, or
- a clear defense backed by repository evidence.

In all cases, remove the `REVIEW:` marker before ending the turn.

**Core principle:** no marker survives, and no critique is hand-waved.

## When to Use

- Human says they left review comments in source files.
- You see comments like `REVIEW: <content>` in code, docs, or config.
- Task is to respond to review feedback directly in the codebase.

Do not use for normal code review where no inline `REVIEW:` markers exist.

## Resolution Contract

For each `REVIEW:` marker, do all of the following:

1. Find the marker and the referenced code.
2. Understand the critique deeply (not keyword matching).
3. Choose one path: `FIX` or `DEFEND`.
4. Apply the chosen path.
5. Remove the `REVIEW:` marker comment.
6. Record a response entry for the final message.

If any marker remains, the task is incomplete.

## Finding Markers Reliably

Search for `REVIEW:` across the workspace, not only one language.

Common forms include:
- `// REVIEW: ...`
- `# REVIEW: ...`
- `/* REVIEW: ... */`
- `<!-- REVIEW: ... -->`

Use broad search first (`REVIEW:`), then inspect context around each hit.

## Deep Critique Evaluation

For each marker, evaluate the critique on these axes:

1. Correctness: Is the claim technically true?
2. Requirements: Does it conflict with explicit task requirements?
3. Risk: Does it affect correctness, security, performance, or maintainability?
4. Scope: Is the suggested change proportional to the problem?
5. Evidence: What code/tests/docs support your conclusion?

Do not dismiss a critique without evidence.

## Decision Rules

Choose `FIX` when any of these are true:
- The critique identifies a real defect or gap.
- The code is unclear and can be improved safely.
- A test/documentation update can close the concern.

Choose `DEFEND` only when all are true:
- The current implementation is intentionally correct for current requirements.
- The critique would regress another requirement, add unjustified complexity, or is factually incorrect.
- You can provide concrete evidence from code/tests/requirements.

`DEFEND` is not "skip". It is a reasoned technical argument.

## Applying `FIX`

When fixing:
- Implement the minimum robust change that addresses the critique.
- Update or add tests when behavior changes or bug risk exists.
- Keep unrelated refactors out of scope.
- Remove the `REVIEW:` marker after the fix is in place.
- Perform any standard verification steps to confirm the fix.

## Applying `DEFEND`

When defending:
- Keep code unchanged except for removing the `REVIEW:` marker (and optional clarifying comment/doc if needed).
- Write a concise defense tied to repository evidence.
- If the marker asks a question, answer it directly in the final response.

A strong defense cites concrete artifacts (paths, tests, constraints), not opinion.

## Completion Gate

Before final response:

1. Verify all `REVIEW:` markers are removed (fresh search, zero results).
2. Verify relevant checks/tests for the touched area.
3. Ensure every former marker has a corresponding response entry.

No zero-marker proof -> no completion claim.

## Final Response Format

End with a per-comment report that covers all resolved markers:

- Marker location and original critique.
- Outcome: `Fixed` or `Defended`.
- What changed (file paths) for `Fixed` outcomes.
- Defense rationale for `Defended` outcomes.
- Verification evidence run for the final state.

Use this structure:

```text
Review response report
- `path/to/file.ext:line` REVIEW: "..."
  Outcome: Fixed
  Changes: `path/to/file.ext`, `path/to/test.ext`
  Verification: <command and key result>

- `path/to/other.ext:line` REVIEW: "..."
  Outcome: Defended
  Defense: <evidence-based rationale>
```

## Red Flags

- Removing markers without addressing the critique.
- Defending without concrete evidence.
- Fixing with large unrelated refactors.
- Reporting completion while `REVIEW:` still exists.
- Answering only some markers.

If any red flag appears, stop and correct before finishing.
