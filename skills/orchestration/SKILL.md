---
name: Orchestrating Implementation
description: Orchestrate complex implementations by planning, delegating to parallel agents, and verifying through iterative loops. Use when implementing features that require coordination across multiple components or when quality assurance through verification is critical.
---

# Orchestrating Implementation

## When to Use This Skill

Use this skill when:
- Implementing a feature from a requirements specification
- Coordinating work across multiple components or systems
- Managing complex implementations with dependencies
- Ensuring quality through code review and testing
- Breaking down large tasks into manageable pieces

**Don't use when:**
- Making a simple, single-file change (just do it)
- Debugging a specific issue (use debug skill)
- Writing requirements (use requirements-writer)

## Core Workflow

```
Requirements → Plan → Implement → Verify → Fix → Re-verify → Complete
              ↑                            |
              └────────── iterate ─────────┘
```

**Key principle:** Never finish without verification passing.

## Reference Materials

**For detailed guidance, see:**
- **[agent-templates.md](agent-templates.md)** - Prompt templates for different agent types
- **[dependencies.md](dependencies.md)** - Strategies for managing task dependencies
- **[verification.md](verification.md)** - Comprehensive verification and iteration strategies

Read these files when you need specific guidance on prompting agents, managing dependencies, or verification loops.

## Step 1: Planning Phase

### Understand the Input

**If you have a specification:**
- Read it thoroughly
- Note scope, constraints, success criteria
- Identify integration points and dependencies

**If you have a vague request:**
- Use `requirements-writer` agent to create a proper spec first
- Then proceed with planning

You can use subagents to explore the codebase and report back to you, to save your own context from being used up.

### Create Implementation Plan

**Plan structure:**
1. Data model changes (what structures?)
2. System logic changes (what systems?)
3. Command interface (what mutations?)
4. Query interface (what reads?)
5. UI changes (what components?)
6. Testing strategy (what tests at each level?)

**Identify dependencies:**
- What must be done first?
- What can be parallelized?
- What's the critical path?

**Break into tasks:**
- Each task = 1 agent can complete independently
- Each task has clear acceptance criteria
- Each task is testable

**Use TodoWrite:** Track your plan and progress. This is essential for visibility.

### Brainstorm (for complex features)

For simple features, skip this. For complex features:

**Launch multiple agents to brainstorm approaches:**

```markdown
Task 1: "Propose implementation approach A for [feature]"
Task 2: "Propose alternative implementation approach B for [feature]"
Task 3: "Compare approaches A and B, recommend best"
```

Run these in parallel (single message, multiple Task calls).

**Review and select:** Choose the best approach based on:
- Simplicity
- Architecture alignment
- Risk level
- Testing complexity

## Step 2: Implementation Phase

### Dispatch Implementation Agents

**For independent tasks (parallel execution):**

Launch multiple `general` agents in parallel (single message, multiple Task calls):

```markdown
Task 1: "Implement data model for [component]. Test your work. Return summary."
Task 2: "Implement system logic for [component]. Test your work. Return summary."
Task 3: "Implement commands for [feature]. Test your work. Return summary."
```

**Agent prompt template:**

```markdown
Implement [specific component] according to this specification:

[Relevant spec excerpt]

**Your scope:**
- Files to modify: [list]
- Files to create: [list]
- DO NOT modify: [exclusions]

**Acceptance criteria:**
- [ ] [Criterion 1]
- [ ] [Criterion 2]

**Testing requirements:**
- Write GdUnit4 tests for [functionality]
- Run `make test` - ensure your tests pass
- Run `timeout 10s make run-headless` - verify no errors

**Verification loop (YOU must do this before returning):**
1. Implement
2. Write tests
3. Run tests and fix failures
4. Run headless game and check output
5. Iterate until working
6. Return summary

**Return:**
- Implementation summary
- Files changed
- Test results
- Any issues or limitations

DO NOT return until your tests pass and the game runs without errors.
```

**For dependent tasks (sequential execution):**

Execute in order, passing context from one agent to the next.

### Monitor Progress

- Use TodoWrite to track which agents are working on what
- Mark tasks as in_progress when agents are launched
- Mark tasks as completed when agents return successfully
- Update the plan as you learn from implementation

### Handle Agent Issues

**If an agent returns with test failures:**

Don't accept it. Launch a follow-up:

```markdown
Fix these test failures:

[Error output]

Specific failures:
1. [Failure 1]
2. [Failure 2]

Run tests again and verify they pass before returning.
```

Repeat until the component works.

## Step 3: Verification Loop

Once all implementation is complete, begin verification.

### Launch Verifiers in Parallel

**Single message with two Task calls:**

**Task 1 - Code Reviewer:**
```markdown
Review the implementation of [feature] against this specification:

[Full spec]

Use git diff [base]..[head] to review changes.

Focus on:
- Architecture alignment
- Code quality and type safety
- Error handling
- Test coverage
- Integration with existing systems

Return detailed code review report.
```

**Task 2 - Tester:**
```markdown
Perform exploratory testing of [feature].

Test scenarios:
1. [Happy path]
2. [Edge cases]
3. [Error conditions]
4. [Integration]

Use the interact skill to test the simulation.

Return comprehensive testing report with any issues found.
```

### Analyze Results

When both agents return:

**Categorize issues by severity:**
- **Critical:** Bugs, broken functionality, data loss risks → MUST fix
- **Important:** Architecture violations, missing features, test gaps → SHOULD fix
- **Minor:** Code style, optimizations, docs → Nice to have

**Decision:**
- Critical or Important issues? → Go to Fix Phase
- Only Minor issues? → Consider accepting or fixing
- No issues? → Complete

### Fix Phase

Launch implementation agents to fix specific issues:

```markdown
Fix these issues from verification:

**Critical:**
1. [Issue description]
   - Location: [file:line]
   - Fix: [suggested fix]

**Important:**
2. [Issue description]
   - Location: [file:line]

Test your fixes. Ensure issues are resolved before returning.
```

### Re-verify

After fixes:
- Run verification again (can be lighter if changes are small)
- Check that fixes didn't introduce new issues
- Iterate until verification passes

**Convergence criteria:**
- No critical or important issues
- All tests passing
- Code reviewer approves
- Tester reports no functional issues

## Step 4: Final Integration

### Final Validation

Run checks yourself:

```bash
make test                          # All tests pass?
timeout 10s make run-headless      # No runtime errors?
git status                         # What changed?
```

### Documentation

Ensure complete:
- Code comments where needed
- Update docs/ if necessary
- Update AGENTS.md if Commands/Queries changed

### Summary for User

Provide comprehensive summary:

```markdown
## Implementation Complete: [Feature Name]

### Summary
[Brief description]

### Changes
- Data Model: [changes]
- Systems: [changes]
- Commands: [changes]
- Queries: [changes]
- UI: [changes]
- Tests: [coverage]

### Verification Results
- ✅ Code Review: [summary]
- ✅ Testing: [summary]
- ✅ All Tests Passing: [count]
- ✅ Game Runs: No errors

### Files Changed
[List]

### Next Steps
[If any]
```

## Common Patterns

### Pattern 1: Simple Feature

**Workflow:**
1. Plan (you do this)
2. Implement (single agent)
3. Verify (code review + testing in parallel)
4. Fix if needed
5. Complete

**Example:** Add a new resource type

### Pattern 2: Multi-Component Feature

**Workflow:**
1. Plan with dependency graph
2. Implement components in parallel
3. Verify (code review + testing in parallel)
4. Fix issues
5. Re-verify
6. Complete

**Example:** New building type
- Agent 1: Data model
- Agent 2: System logic
- Agent 3: Commands/Queries
- Agent 4: UI
- Agent 5: Tests

All in parallel (respecting dependencies).

### Pattern 3: Complex Feature

**Workflow:**
1. Brainstorm approaches (parallel agents)
2. Review and select approach
3. Create detailed plan
4. Have plan reviewed by agent
5. Implement in phases (each phase: implement → verify → fix)
6. Final verification
7. Complete

**Example:** New game mechanic with multiple systems interaction

## Context Management

**Save your context by:**
- Not reading large codebases yourself (delegate)
- Not writing code yourself (delegate)
- Not debugging yourself (use debug agent)
- Focusing on coordination, not execution

**Use your context for:**
- Reading specifications
- Creating plans
- Reviewing agent summaries
- Making decisions
- Final validation

## Quality Checklist

Before marking complete, verify:

- ✅ All tests passing (`make test`)
- ✅ Game runs without errors (`timeout 10s make run-headless`)
- ✅ Code review approved (no critical/important issues)
- ✅ Exploratory testing passed
- ✅ Type safety enforced (no warnings)
- ✅ Architecture aligned (Architecture v2)
- ✅ Test coverage adequate

**Do not complete until all checked.**

## Critical Rules

**DO:**
- Use TodoWrite to track everything
- Dispatch parallel agents in single message (multiple Task calls)
- Give agents clear scope and acceptance criteria
- Require agents to test their own work
- Verify with BOTH code-reviewer AND tester
- Iterate verification until clean
- Provide comprehensive final summary

**DON'T:**
- Do deep implementation yourself
- Skip planning phase
- Accept untested code
- Skip verification
- Finish with known issues
- Summarize when full details needed

## Debugging Support

If agents can't solve an issue:

```markdown
Debug this issue:

[Problem description]
[Error messages]
[Context]

Use the debug agent interface to investigate runtime state.

Return diagnosis and suggested fix.
```

Then use the diagnosis to guide implementation agents.

## Example: Implementing "Wood Resource"

**1. Planning:**
- Simple feature, no brainstorming needed
- Plan: Add to SimConstants, update relevant systems, add tests

**2. Implementation:**
- Single agent: "Implement wood resource per this plan: [plan]"

**3. Verification:**
- Parallel: code-reviewer + tester (single message, 2 tasks)
- Both return clean

**4. Complete:**
- Final validation
- Summary to user

## Example: Implementing "Logistics Contracts"

**1. Planning:**
- Complex feature, brainstorm approaches
- Agents propose and compare approaches
- Select best, create detailed plan with dependencies

**2. Phase 1 - Data:**
- Agent: Implement Contract data class
- Verify → fix → clean

**3. Phase 2 - Logic:**
- Agent 1: Contract system
- Agent 2: Truck integration
- (Parallel execution)
- Verify → fix → clean

**4. Phase 3 - Interface:**
- Agent 1: Commands
- Agent 2: Queries
- Agent 3: UI
- (Parallel execution)
- Verify → fix → clean

**5. Final Verification:**
- Code review + testing (parallel)
- Fix issues
- Re-verify
- Complete

## Troubleshooting

**Problem: Agent returns broken code**
- Solution: Don't accept it. Send follow-up with error output and ask for fixes.

**Problem: Verification finds critical issues**
- Solution: Launch implementation agents to fix. Re-verify after.

**Problem: Agents stuck on a bug**
- Solution: Use debug agent to investigate, then guide implementation with diagnosis.

**Problem: Too many parallel agents overwhelming**
- Solution: Break into smaller batches or phases. Sequence dependencies.

**Problem: Losing track of progress**
- Solution: Use TodoWrite religiously. Mark tasks as in_progress and completed.

## Remember

You are the orchestrator:
- **Plan** the strategy
- **Delegate** the work
- **Coordinate** timing
- **Verify** quality
- **Deliver** working code

Success = working, tested, reviewed code. Not how much you personally wrote.
