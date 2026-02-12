---
name: implementation-walkthrough
description: Use when completing a feature, bugfix, or significant implementation and need to summarize what changed for your human collaborator
---

# Implementation Walkthrough

## Overview

After completing an implementation, provide a structured walkthrough that lets your human collaborator understand, navigate, and verify your work.

**Core principle:** Show code, not descriptions of code. Provide commands, not claims of success. Keep it high-level, this is an overview.

## Walkthrough Structure

### 1. Overview (2-3 sentences)
What was implemented and why.

### 2. Key Changes

For each significant change, provide:
- **File reference with line numbers**: `src/auth/middleware.ts:25-45`
- **What changed**: Brief description
- **Code snippet**: The actual key code (not a description), but only what is necessary to understand the change.

```markdown
**`src/middleware/auth.ts:15-35`** - JWT verification logic

\`\`\`typescript
export const authMiddleware = (req: Request, res: Response, next: NextFunction) => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  if (!token) {
    return res.status(401).json({ error: 'Missing token' });
  }
  // ... key logic
};
\`\`\`
```

**Show 10-30 lines of key code per major change.** Don't dump entire files.

### 3. Verification Performed

Provide reproducible verification with expected output:

```markdown
**Commands run:**
\`\`\`bash
npm test                    # 78 tests, 0 failures
npm run lint               # No errors
\`\`\`

**Manual verification:**
\`\`\`bash
curl -H "Authorization: Bearer $TOKEN" http://localhost:3000/api/protected
# Expected: 200 OK, returns { "user": "..." }
\`\`\`
```

**Always include:**
- The command to run
- Expected output or result

### 4. Challenges Encountered

Technical decisions, tradeoffs, or problems solved:

```markdown
**Circular reference handling**: Search results had circular refs that broke 
`JSON.stringify`. Solved with custom serializer tracking seen objects.
See `src/formatters/json.ts:42-58`.
```

### 5. Not Completed

Explicitly call out what wasn't done:

```markdown
**NOT COMPLETED:**
- Streaming JSON output - blocked by API limitation (no streaming support)
- Rate limiting - deferred, requires Redis dependency
```

**Don't bury this** - make it prominent so collaborator knows the scope.

## Quick Reference

| Section | Must Include |
|---------|--------------|
| Overview | What + why in 2-3 sentences |
| Key Changes | File:line refs + code snippets |
| Verification | Runnable commands + expected output |
| Challenges | Decisions made + where to find code |
| Not Completed | Explicit list of deferred/blocked items |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| "Added 80 lines of auth logic" | Show the key 15-20 lines |
| "All tests pass" | Provide: `npm test` + expected result |
| "Manually tested and it works" | Show command + expected output |
| Challenges at bottom | Put challenges prominently, they matter |
| "Streaming deferred" buried in prose | Use explicit "NOT COMPLETED" section |
| No line numbers | Always: `file.ts:25-40` format |
