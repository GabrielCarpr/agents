# Agent Prompt Templates

This document provides detailed templates for different types of agent tasks.

## Implementation Agent Template

```markdown
Implement [specific component/feature] according to this specification:

[Specification excerpt - be specific about what this agent is building]

**Your scope:**
- Files to modify: [list specific files]
- Files to create: [list new files needed]
- DO NOT modify: [list files to avoid]

**Acceptance criteria:**
- [ ] [Specific, testable criterion 1]
- [ ] [Specific, testable criterion 2]
- [ ] [Specific, testable criterion 3]

**Technical requirements:**
- Follow Architecture v2 patterns (Data → Systems → Commands/Queries)
- Use strict typing (no Variant types, typed arrays)
- Handle errors explicitly (no silent failures)
- [Any specific architectural constraints]

**Testing requirements:**
- Write GdUnit4 tests for [specific functionality]
- Test coverage expectations:
  - Unit tests: [what to test]
  - Integration tests: [what to test]
- Run `make test` - ensure all tests pass
- Run `timeout 10s make run-headless` - verify no runtime errors

**Verification loop (critical - you MUST do this before returning):**
1. Implement the functionality
2. Write comprehensive tests
3. Run `make test` and fix any failures
4. Run `timeout 10s make run-headless` and check output for errors
5. Iterate steps 3-4 until clean
6. Only then return your summary

**Expected return:**
- Summary of implementation approach
- List of files created/modified
- Test results (X tests passing)
- Headless run result (no errors / errors found and fixed)
- Any limitations or known issues
- Suggestions for future improvements (optional)

**CRITICAL:** Do NOT return until your tests pass and the game runs without errors.
Your work will be rejected if you return broken code.
```

## Brainstorming Agent Template

```markdown
Propose an implementation approach for this feature:

[Full specification]

**Your task:**
Analyze this specification and propose a complete implementation strategy.

**Consider:**
1. **Data Model:**
   - What new data structures are needed?
   - What existing structures need modification?
   - How does data flow through the system?

2. **System Architecture:**
   - Which systems need updates?
   - Do we need new systems?
   - How do systems interact?
   - Tick sequence considerations?

3. **Interface Design:**
   - What commands (mutations) are needed?
   - What queries (reads) are needed?
   - How does UI interact with the simulation?

4. **Testing Strategy:**
   - What needs unit tests?
   - What needs integration tests?
   - How to test the full workflow?

5. **Risk Assessment:**
   - What could go wrong?
   - What's the highest risk area?
   - How to mitigate risks?

6. **Trade-offs:**
   - What alternative approaches exist?
   - Why is your approach better?
   - What are the downsides?

**Return:**
A detailed implementation plan with:
- Component breakdown
- Dependency graph (what must be built first)
- Testing strategy
- Risk areas and mitigations
- Rationale for key decisions
```

## Plan Review Agent Template

```markdown
Review this implementation plan for feasibility and quality:

**Specification:**
[Original specification]

**Proposed Plan:**
[Implementation plan to review]

**Your task:**
Critically evaluate this plan before implementation begins.

**Review criteria:**

1. **Completeness:**
   - Does it address all requirements?
   - Are any requirements missed?
   - Are edge cases considered?

2. **Architecture Alignment:**
   - Does it follow Architecture v2 (Data → Systems → Commands/Queries)?
   - Are components in the right places (data/, systems/, commands/, queries/)?
   - Does it respect the autoload pattern (Sim.world access)?

3. **Feasibility:**
   - Can this actually be built?
   - Are there technical blockers?
   - Are dependencies correctly identified?

4. **Testing:**
   - Is the testing strategy adequate?
   - Can we verify each requirement?
   - Are integration points testable?

5. **Risk Assessment:**
   - What could go wrong?
   - Are high-risk areas identified?
   - Are mitigations adequate?

6. **Alternatives:**
   - Is this the best approach?
   - Should we consider alternatives?
   - Are trade-offs justified?

**Return:**
- Overall assessment (Approve / Approve with changes / Major concerns)
- Strengths of the plan
- Issues or gaps (categorized by severity)
- Specific recommendations for improvement
- Alternative approaches to consider (if any)
```

## Fix Issues Agent Template

```markdown
Fix these issues identified during verification:

**Original specification:**
[Brief spec context]

**Issues to fix:**

### Critical Issues (MUST fix)
1. **[Issue title]**
   - Location: [file:line]
   - Problem: [description]
   - Expected behavior: [what should happen]
   - Actual behavior: [what happens]
   - Suggested fix: [if available]

[Repeat for each critical issue]

### Important Issues (SHOULD fix)
[Same structure]

**Constraints:**
- DO NOT introduce new issues
- DO NOT change unrelated code
- Maintain test coverage

**Verification requirements:**
- Run `make test` - all tests must pass
- Run `timeout 10s make run-headless` - no errors
- Verify the specific issues are resolved

**Return:**
- Summary of fixes applied
- Confirmation that each issue is resolved
- Test results
- Any new considerations or risks introduced
```

## Debugging Agent Template

```markdown
Debug this issue in the Ironbound simulation:

**Problem description:**
[What's wrong? What's the expected vs actual behavior?]

**Error messages:**
[Stack traces, error output, test failures]

**Context:**
- Feature being implemented: [feature name]
- Component affected: [system/command/query]
- When does it occur: [during tick? on command? always?]

**Your task:**
1. Use the `debug` skill to load the `interact` skill
2. Start the game and interact with it via the agent interface
3. Reproduce the issue
4. Investigate the runtime state to understand root cause
5. Propose a fix with rationale

**Investigation approach:**
- Check simulation state with queries
- Trace data flow through systems
- Look for timing issues (tick ordering)
- Verify command/query behavior
- Check for data inconsistencies

**Return:**
- Root cause analysis (what's actually wrong?)
- Why it's happening (architectural/logical reason)
- Proposed fix (be specific - file, function, change)
- How to verify the fix works
- Any additional considerations
```

## Comparative Review Agent Template

```markdown
Compare these implementation approaches and recommend the best:

**Specification:**
[Original spec]

**Approach A:**
[First proposed approach]

**Approach B:**
[Second proposed approach]

[Additional approaches if any]

**Your task:**
Objectively compare these approaches and recommend the best one.

**Comparison criteria:**

1. **Simplicity:**
   - Which is easier to understand?
   - Which has fewer moving parts?
   - Which is easier to maintain?

2. **Architecture Alignment:**
   - Which better follows Architecture v2?
   - Which integrates better with existing code?
   - Which is more consistent with project patterns?

3. **Testability:**
   - Which is easier to test?
   - Which has better test coverage potential?
   - Which has fewer test edge cases?

4. **Performance:**
   - Which is more efficient?
   - Which scales better?
   - Which has lower complexity?

5. **Risk:**
   - Which has fewer failure modes?
   - Which is more robust?
   - Which is easier to debug?

6. **Extensibility:**
   - Which is easier to extend later?
   - Which is more flexible?
   - Which has better future-proofing?

**Return:**
- Side-by-side comparison table
- Pros and cons of each approach
- Clear recommendation with detailed rationale
- Any hybrid approach that combines strengths
- Implementation considerations for recommended approach
```

## Testing Agent Prompt Template

```markdown
Perform comprehensive exploratory testing of [feature]:

**Feature description:**
[What was implemented]

**Specification reference:**
[Link to spec or key requirements]

**Your task:**
Test this feature thoroughly using the interact skill.

**Pre-testing setup:**
1. Load the `interact` skill
2. Verify game connection (meta-ping)
3. Check current game state

**Test scenarios (minimum):**

1. **Happy Path:**
   - [Scenario description]
   - Expected: [what should happen]

2. **Edge Case 1:**
   - [Scenario description]
   - Expected: [what should happen]

3. **Edge Case 2:**
   - [Scenario description]
   - Expected: [what should happen]

4. **Error Condition:**
   - [Scenario description]
   - Expected: [how errors should be handled]

5. **Integration:**
   - [How it works with other systems]
   - Expected: [what should happen]

**For each scenario:**
- Capture initial state (queries)
- Perform actions (commands)
- Capture final state (queries)
- Verify expectations
- Document any issues

**Return:**
Comprehensive testing report with:
- Test summary (X scenarios, Y passed, Z failed)
- Detailed results for each scenario
- Issues found (categorized by severity)
- Evidence (query results, logs)
- Reproduction steps for issues
- Overall assessment (PASS/FAIL)
```

## Usage Notes

**When to use each template:**

- **Implementation:** Most common - use for all feature implementation tasks
- **Brainstorming:** Use for complex features where approach isn't obvious
- **Plan Review:** Use before starting implementation of complex features
- **Fix Issues:** Use after verification finds problems
- **Debugging:** Use when implementation agents are stuck on a bug
- **Comparative Review:** Use when multiple approaches are proposed
- **Testing:** Use for exploratory testing (different from automated tests)

**Customization:**

These are templates, not scripts. Customize for your specific context:
- Add domain-specific requirements
- Adjust scope to task size
- Include relevant architectural constraints
- Reference specific files or systems
- Add project-specific quality standards
