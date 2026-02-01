# Verification Strategies

This guide explains how to verify implementation quality through code review and testing.

## The Verification Loop

```
Implementation → Verification → Issues? → Fix → Re-verify → Complete
                                    ↓
                                   No
                                    ↓
                                Complete
```

**Golden rule:** Never finish without clean verification.

## Dual Verification Approach

Always verify with BOTH:
1. **Code Reviewer** - Static analysis, architecture, quality
2. **Tester** - Dynamic testing, functionality, integration

**Why both?**
- Code review finds structural and design issues
- Testing finds behavioral and integration issues
- Together they provide comprehensive coverage

**Run them in parallel** for efficiency (single message, two Task calls).

## Code Review Verification

### What Code Review Catches

**Architecture issues:**
- Violation of Architecture v2 patterns
- Components in wrong places
- Improper use of autoload pattern
- Breaking separation of concerns

**Code quality:**
- Type safety violations
- Missing error handling
- Poor naming or structure
- Duplicated code
- Overly complex logic

**Integration concerns:**
- Breaking changes to existing APIs
- Missing backward compatibility
- Conflicts with existing code
- Inconsistent patterns

**Test coverage:**
- Missing tests for edge cases
- Tests that don't test behavior
- Insufficient integration tests

### Code Review Agent Setup

**Provide the agent with:**
1. Original specification or plan
2. Git range to review (base SHA to HEAD SHA)
3. Focus areas specific to the feature
4. Architecture constraints

**Example:**

```markdown
Review the implementation of Logistics Contracts against this specification:

[Specification]

Git range: abc123..def456

Focus on:
- Architecture alignment (is ContractSystem in the right place?)
- Data flow through autoload (using Sim.world correctly?)
- Command/Query separation (mutations vs reads)
- Test coverage (are all contracts edge cases tested?)

Return detailed code review with issues categorized by severity.
```

### Interpreting Code Review Results

**Critical issues:**
- Bugs that cause crashes or data loss
- Security vulnerabilities
- Broken functionality
- **Action:** MUST fix before completion

**Important issues:**
- Architecture violations
- Missing features from spec
- Poor error handling
- Inadequate test coverage
- **Action:** SHOULD fix unless justified

**Minor issues:**
- Code style inconsistencies
- Optimization opportunities
- Documentation gaps
- **Action:** Optional, nice to have

**Example categorization:**

```
Critical:
- ContractSystem.tick() can crash if contracts array is null (line 45)
  → Fix: Add null check

Important:
- Missing tests for contract cancellation edge case
  → Fix: Add test for cancel-while-in-progress

Minor:
- Could use type inference instead of explicit type (line 78)
  → Optional: Improve code style
```

## Testing Verification

### What Testing Catches

**Functional issues:**
- Features don't work as specified
- Commands fail or produce wrong results
- Queries return incorrect data

**Integration issues:**
- Systems don't interact correctly
- Data doesn't flow properly
- State transitions are broken

**Edge cases:**
- Boundary conditions fail
- Error conditions not handled
- Unexpected inputs cause crashes

**Performance:**
- Slowdowns or hangs
- Resource leaks
- Inefficient algorithms

### Testing Agent Setup

**Provide the agent with:**
1. Feature description
2. Expected behaviors from spec
3. Specific scenarios to test
4. Success criteria

**Example:**

```markdown
Perform exploratory testing of Logistics Contracts.

Test scenarios:

1. Happy Path:
   - Create contract from Province A to B for 100 grain
   - Verify truck picks up goods
   - Verify truck delivers to destination
   - Verify contract marked complete

2. Edge Case - Insufficient Supply:
   - Create contract for 100 grain when only 50 available
   - Verify contract creation fails OR partial fulfillment works

3. Edge Case - No Available Trucks:
   - Create contract when all trucks busy
   - Verify contract queues OR creation fails gracefully

4. Error Condition - Invalid Province:
   - Create contract with non-existent province ID
   - Verify error is returned

5. Integration - Multiple Contracts:
   - Create 5 contracts simultaneously
   - Verify trucks prioritize correctly
   - Verify all contracts eventually complete

Use the interact skill to test. Return comprehensive report.
```

### Interpreting Testing Results

**Severity levels:**

**Critical:**
- Feature completely broken
- Crashes or errors during normal use
- Data corruption
- **Action:** MUST fix

**Important:**
- Feature partially broken
- Edge cases fail
- Poor error handling
- Integration issues
- **Action:** SHOULD fix

**Minor:**
- Cosmetic issues
- Suboptimal behavior
- Missing conveniences
- **Action:** Optional

**Example interpretation:**

```
FAIL: Happy Path - Contract not completing
  Severity: Critical
  Issue: Truck picks up goods but never marks contract complete
  → Must fix before completion

FAIL: Edge Case - Insufficient Supply
  Severity: Important
  Issue: Contract creation succeeds but truck gets stuck
  → Should fix, workaround: validate supply before contract

PASS: Edge Case - No Available Trucks
  Severity: N/A
  Contract correctly queues until truck available

FAIL: Error Condition - Invalid Province
  Severity: Important
  Issue: Returns generic error, not helpful message
  → Should improve error message

PASS: Integration - Multiple Contracts
  Severity: N/A
  All contracts complete correctly
```

## Verification Loop Patterns

### Pattern 1: Single Verification Pass (Simple Features)

```
Implementation → Verify (code review + testing) → Issues? → Fix → Complete
                                                      ↓
                                                     No
                                                      ↓
                                                  Complete
```

**When to use:**
- Simple features
- Low risk
- Confident implementation

**Process:**
1. Implementation complete
2. Run code review + testing in parallel
3. If clean, complete
4. If issues, fix and verify fixes (lightweight re-check)

### Pattern 2: Iterative Verification (Complex Features)

```
Implementation → Verify → Fix → Verify → Fix → Verify → Complete
```

**When to use:**
- Complex features
- High risk areas
- First-time implementation of a pattern

**Process:**
1. Implementation complete
2. Run full verification
3. Fix all critical and important issues
4. Run full verification again
5. Repeat until clean or only minor issues remain

**Why iterate?**
- Fixes can introduce new issues
- Complex features have hidden edge cases
- High confidence before completion

### Pattern 3: Phased Verification (Large Features)

```
Phase 1: Implement + Verify → Clean
Phase 2: Implement + Verify → Clean
Phase 3: Implement + Verify → Clean
Final: Full Integration Verify → Clean → Complete
```

**When to use:**
- Multi-phase implementations
- Features with clear separation

**Process:**
1. Implement Phase 1 (e.g., data layer)
2. Verify Phase 1
3. Fix Phase 1 issues until clean
4. Implement Phase 2 (e.g., logic layer)
5. Verify Phase 2
6. Fix Phase 2 issues until clean
7. Continue for all phases
8. Final full integration verification

**Why phase verification?**
- Catch issues early before building on them
- Reduce rework
- Higher confidence at each layer

## Combining Verification Results

After both code review and testing return, synthesize findings:

### Create Unified Issue List

**Merge issues from both sources:**

```markdown
Critical Issues:
1. ContractSystem can crash on null array (code review)
2. Happy path test fails - contracts not completing (testing)

Important Issues:
3. Missing test for contract cancellation (code review)
4. Error messages not helpful for invalid province (testing)
5. Architecture: ContractSystem should use Command pattern (code review)

Minor Issues:
6. Could use type inference (code review)
7. UI could show contract progress percentage (testing)
```

### Prioritize Fixes

**Order by impact:**
1. Critical issues (blocking bugs)
2. Important issues (quality/completeness)
3. Minor issues (nice to have)

**Consider effort:**
- Quick fixes (< 10 min): Do immediately
- Medium fixes (< 1 hour): Do before completion
- Large fixes (> 1 hour): Consider deferring if minor severity

### Dispatch Fix Agents

**Group related fixes:**

```markdown
Agent 1: Fix critical issues 1 and 2
  - Add null check in ContractSystem
  - Fix contract completion logic

Agent 2: Fix important issues 3 and 4
  - Add cancellation test
  - Improve error messages

[Optionally skip minor issues or defer to future work]
```

**Run fix agents, then re-verify.**

## Re-Verification Strategies

### Full Re-Verification

**When:** Major fixes, first iteration, high risk

**Process:**
- Run full code review again
- Run full test suite again
- Check that fixes didn't introduce new issues

**Cost:** High (time and context)
**Benefit:** High confidence

### Targeted Re-Verification

**When:** Minor fixes, late iterations, low risk

**Process:**
- Review only changed files
- Test only affected scenarios
- Spot-check integration

**Cost:** Low
**Benefit:** Adequate confidence for small changes

### Self-Verification by Fix Agent

**When:** Very minor fixes, late iterations

**Process:**
- Fix agent verifies their own fix (runs tests)
- Orchestrator checks test output
- No separate verification agent needed

**Cost:** Minimal
**Benefit:** Fast iteration

**Example:**

```markdown
Fix this critical issue:

[Issue description]

After fixing:
1. Run `make test` and ensure tests pass
2. Run `timeout 10s make run-headless` and check for errors
3. Verify the specific issue is resolved

Return summary with test results.
```

## Convergence Criteria

**When to stop iterating and mark complete:**

### Must-Have Criteria

- ✅ All critical issues resolved
- ✅ All tests passing (`make test`)
- ✅ Game runs without errors (`make run-headless`)
- ✅ Code reviewer approves (no critical/important issues)
- ✅ Tester reports functional (no critical/important issues)

### Should-Have Criteria

- ✅ All important issues resolved or justified
- ✅ Test coverage adequate for feature
- ✅ Architecture aligned with project patterns
- ✅ Documentation complete

### Nice-to-Have Criteria

- ✅ All minor issues resolved
- ✅ Code style perfect
- ✅ Optimizations applied

**Decision:**
- Must-Have not met? → Keep iterating
- Must-Have met, Should-Have not? → Discuss with user
- Should-Have met? → Can complete

## Verification Anti-Patterns

### ❌ Anti-Pattern 1: Skipping Verification

**What:** Implement and immediately mark complete

**Why it's bad:**
- No quality check
- Issues discovered later (more expensive)
- User loses trust

**Fix:** Always verify before completion

### ❌ Anti-Pattern 2: Accepting "Mostly Working"

**What:** Verification finds issues, but you complete anyway

**Why it's bad:**
- Technical debt accumulates
- Bugs in production
- Incomplete features

**Fix:** Fix critical and important issues before completion

### ❌ Anti-Pattern 3: Infinite Iteration

**What:** Keep finding minor issues, never complete

**Why it's bad:**
- Diminishing returns
- Wastes time
- Blocks progress

**Fix:** Use convergence criteria - know when to stop

### ❌ Anti-Pattern 4: Single Verification Type

**What:** Only code review OR only testing, not both

**Why it's bad:**
- Incomplete coverage
- Misses whole classes of issues

**Fix:** Always do both code review AND testing

### ❌ Anti-Pattern 5: Trusting Implementation Without Verification

**What:** Agent says "tests pass", you believe them

**Why it's bad:**
- Agents can make mistakes
- Tests might not cover the right things
- False confidence

**Fix:** Verify with independent agents (code-reviewer, tester)

## Verification Checklist

Before marking a feature complete, verify:

**Code Review:**
- [ ] Architecture aligned with Architecture v2
- [ ] Type safety enforced (no warnings)
- [ ] Error handling comprehensive
- [ ] No breaking changes (or documented)
- [ ] Test coverage adequate
- [ ] Code quality high (readable, maintainable)

**Testing:**
- [ ] Happy path works
- [ ] Edge cases handled
- [ ] Error conditions handled gracefully
- [ ] Integration with other systems works
- [ ] No crashes or errors in exploratory testing

**Implementation:**
- [ ] All requirements from spec met
- [ ] All acceptance criteria met
- [ ] No regressions (existing features still work)

**Quality:**
- [ ] All tests passing (`make test`)
- [ ] Game runs without errors (`make run-headless`)
- [ ] No critical or important issues remain
- [ ] Documentation complete (if needed)

**Only mark complete when all boxes checked.**

## Example Verification Session

**Feature:** Logistics Contracts

### Round 1: Initial Verification

**Dispatch (parallel):**
- Code review agent
- Testing agent

**Results:**

Code Review:
- Critical: Null pointer risk in ContractSystem:45
- Important: Missing cancellation tests
- Important: Command should validate province exists
- Minor: Could use type inference

Testing:
- Critical: Happy path fails - contracts never complete
- Important: Invalid province error message unhelpful
- Pass: Contract queuing works
- Pass: Multiple contracts work

### Round 1: Fixes

**Fix agent:**
- Fix null pointer issue
- Fix contract completion logic
- Add province validation
- Improve error message
- Add cancellation tests

**Fix agent reports:** All fixes applied, tests pass

### Round 2: Re-Verification (Targeted)

**Code review (files changed by fixes only):**
- Clean - fixes look good

**Testing (targeted scenarios):**
- Pass: Happy path now works
- Pass: Invalid province gives helpful error
- Pass: Cancellation works

**Result:** Clean - only minor issues remain

### Decision

**Criteria check:**
- ✅ Critical issues resolved
- ✅ Important issues resolved
- ✅ Tests passing
- ✅ Game runs without errors
- ✅ Code review approves
- ✅ Testing passes

**Action:** Mark complete (defer minor type inference improvement)

## Summary

**Verification is not optional.**

**Key principles:**
1. Always verify with BOTH code review AND testing
2. Run verifiers in parallel for efficiency
3. Fix critical and important issues before completion
4. Re-verify after fixes
5. Iterate until convergence criteria met
6. Know when to stop (don't chase minor issues forever)

**Remember:** Verification protects quality. The time spent verifying is far less than the time spent fixing bugs in production or dealing with technical debt.
