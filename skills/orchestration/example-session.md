# Example Orchestration Session

This document walks through a complete orchestration session for implementing a complex feature.

## Feature: Logistics Contracts System

**Specification summary:**
- Players can create contracts to transport goods between provinces
- Trucks bid on contracts based on profitability
- Contracts have origin, destination, goods type, quantity, payment
- Failed contracts return payment to player
- System tracks contract status (pending, in-progress, completed, failed)

**Complexity:** High
- Multiple components (data, systems, commands, queries, UI)
- Integration with existing logistics system
- Economic implications (pricing, payment)
- Multiple edge cases

## Phase 1: Planning

### Step 1.1: Analyze the Specification

**Orchestrator reads the spec and identifies:**

**Data model needs:**
- Contract data class (origin, destination, goods, quantity, payment, status)
- Truck needs contract_id field
- SimWorld needs contracts array

**System logic needs:**
- ContractSystem (generate, assign, track contracts)
- LogisticsSystem updates (trucks fulfill contracts)
- Economic integration (payment flows)

**Interface needs:**
- Commands: create_contract, cancel_contract
- Queries: get_contracts, get_contract_status
- UI: Contract management panel

**Testing needs:**
- Unit tests for Contract class
- Unit tests for ContractSystem
- Integration tests for full workflow
- Exploratory testing for edge cases

### Step 1.2: Brainstorm Approaches (Complex Feature → Brainstorm)

**Orchestrator launches 2 agents in parallel:**

```markdown
# Agent 1
Propose implementation approach A for Logistics Contracts system:

[Full spec]

Focus on: How to integrate with existing LogisticsSystem without breaking it.
Return detailed plan.

# Agent 2
Propose ALTERNATIVE implementation approach B for Logistics Contracts:

[Full spec]

Focus on: Different architectural approach - consider if contracts should be
first-class citizens or just data on trucks.
Return detailed plan.
```

**Agent 1 returns:** "Contracts as separate entities, ContractSystem manages them,
trucks reference contracts by ID. Clean separation."

**Agent 2 returns:** "Contracts embedded in trucks, no separate system. Simpler but
less flexible."

### Step 1.3: Review Approaches

**Orchestrator launches review agent:**

```markdown
Compare these two approaches and recommend best:

[Spec]

Approach A: [Agent 1's plan]
Approach B: [Agent 2's plan]

Evaluate: architecture alignment, testability, extensibility.
Return recommendation.
```

**Review agent returns:** "Approach A is better. Separate contracts allow:
- Multiple trucks per contract (future)
- Contract queuing when no trucks available
- Easier testing (no truck coupling)
- Better aligns with Architecture v2 (separate data + system)"

### Step 1.4: Create Detailed Plan

**Orchestrator creates plan based on Approach A:**

```markdown
Implementation Plan: Logistics Contracts

## Phase 1: Data Layer (Parallel)
Task 1: Create Contract.gd data class
  - Fields: id, origin_id, dest_id, goods_type, quantity, payment, status, assigned_truck_id
  - Methods: none (pure data)
  
Task 2: Update Truck.gd
  - Add: current_contract_id: int = -1
  
Task 3: Write tests for Contract.gd
  - Test: instantiation
  - Test: field access

Dependencies: None (all parallel)

## Phase 2: World Integration (Sequential)
Task 4: Update SimWorld.gd
  - Add: contracts: Array[Contract] = []
  - Add: next_contract_id: int = 0
  
Dependencies: Needs Contract.gd from Task 1

## Phase 3: Core System (Sequential)
Task 5: Create ContractSystem.gd
  - tick(): Check pending contracts, assign to available trucks
  - complete_contract(contract_id): Mark complete, pay out
  - fail_contract(contract_id): Mark failed, refund payment
  
Dependencies: Needs Contract.gd, Truck.gd, SimWorld updated

## Phase 4: Extensions (Parallel)
Task 6: Update LogisticsSystem.gd
  - Trucks check current_contract_id
  - If assigned, fulfill contract instead of free roam
  - On delivery, call ContractSystem.complete_contract()
  
Task 7: Create ContractCommands.gd
  - create_contract(faction_id, origin, dest, goods, qty, payment)
  - cancel_contract(contract_id)
  
Task 8: Create ContractQueries.gd
  - get_contracts(faction_id)
  - get_contract_status(contract_id)
  
Task 9: Write tests for ContractSystem.gd
  - Test: contract assignment
  - Test: contract completion
  - Test: contract failure
  
Dependencies: All need Task 5

## Phase 5: UI (Sequential)
Task 10: Create ContractUI.gd
  - Display contracts list
  - Create contract form
  - Show contract status
  
Dependencies: Needs Tasks 7 and 8

## Phase 6: Integration Testing
Task 11: Write integration tests
  - Test: Full workflow (create → assign → fulfill → complete)
  - Test: Edge case (no trucks available)
  - Test: Edge case (insufficient goods)
  - Test: Cancellation
  
Dependencies: Everything

Critical Path: 1 → 4 → 5 → 6 → 10 → 11 (6 sequential steps)
Parallelism: Phases 1, 4 have parallel work
```

**Orchestrator uses TodoWrite to track plan:**

```markdown
- [ ] Phase 1a: Contract class (Agent 1)
- [ ] Phase 1b: Truck updates (Agent 2)
- [ ] Phase 1c: Contract tests (Agent 3)
- [ ] Phase 2: SimWorld integration (Agent 4)
- [ ] Phase 3: ContractSystem (Agent 5)
- [ ] Phase 4a: LogisticsSystem updates (Agent 6)
- [ ] Phase 4b: Commands (Agent 7)
- [ ] Phase 4c: Queries (Agent 8)
- [ ] Phase 4d: ContractSystem tests (Agent 9)
- [ ] Phase 5: UI (Agent 10)
- [ ] Phase 6: Integration tests (Agent 11)
```

## Phase 2: Implementation

### Round 1: Phase 1 - Data Layer

**Orchestrator dispatches 3 agents in parallel (single message):**

```markdown
# Task 1 - Agent 1
Implement Contract.gd data class in sim/data/Contract.gd

[Specification for Contract class]

Your scope: Create Contract.gd only

Acceptance criteria:
- [ ] Contract.gd exists in sim/data/
- [ ] All fields present with correct types
- [ ] Follows Architecture v2 (RefCounted data class)

Test: Create simple test instantiating Contract

Run `make test` before returning.

Return: Summary of implementation

---

# Task 2 - Agent 2
Update Truck.gd to add contract support

[Specification for Truck update]

Your scope: Add current_contract_id field to Truck.gd

Acceptance criteria:
- [ ] current_contract_id field added
- [ ] Default value is -1 (no contract)
- [ ] Type is int

Test: Verify Truck still works with new field

Run `make test` before returning.

Return: Summary of changes

---

# Task 3 - Agent 3
Write tests for Contract.gd data class

[Specification]

Your scope: Create test/sim/TestContract.gd

Acceptance criteria:
- [ ] Test file created
- [ ] Tests instantiation
- [ ] Tests field access
- [ ] All tests pass

Run `make test` before returning.

Return: Test results
```

**Agents return:**
- Agent 1: "Contract.gd created, 7 fields, typed correctly, tests pass"
- Agent 2: "Truck.gd updated, field added, existing tests pass"
- Agent 3: "TestContract.gd created, 3 tests, all pass"

**Orchestrator marks Phase 1 complete in TodoWrite.**

### Round 2: Phase 2 - World Integration

**Orchestrator dispatches Agent 4:**

```markdown
Update SimWorld.gd to integrate contracts

[Specification]

Context from Phase 1:
- Contract.gd exists with these fields: [fields]
- Truck.gd has current_contract_id field

Your scope: Update sim/SimWorld.gd

Acceptance criteria:
- [ ] contracts: Array[Contract] field added
- [ ] next_contract_id: int field added
- [ ] Follows existing SimWorld patterns

Test: Verify SimWorld initializes correctly

Run `make test` before returning.

Return: Summary of changes
```

**Agent 4 returns:** "SimWorld updated, contracts array added, tests pass"

**Orchestrator marks Phase 2 complete.**

### Round 3: Phase 3 - Core System

**Orchestrator dispatches Agent 5:**

```markdown
Implement ContractSystem.gd

[Full specification for ContractSystem]

Context:
- Contract.gd: [structure]
- SimWorld.contracts: [array]
- Truck.current_contract_id: [field]

Your scope: Create sim/systems/ContractSystem.gd

Acceptance criteria:
- [ ] tick() method assigns contracts to trucks
- [ ] complete_contract() marks complete, pays out
- [ ] fail_contract() marks failed, refunds
- [ ] Uses Sim.world for data access (autoload pattern)
- [ ] Stateless system (no instance variables)

Testing:
- Write basic tests for each method
- Run `make test`

Return: Implementation summary and test results
```

**Agent 5 returns:** "ContractSystem created, all methods implemented, 5 tests, all pass"

**Orchestrator marks Phase 3 complete.**

### Round 4: Phase 4 - Extensions (Parallel)

**Orchestrator dispatches 4 agents in parallel:**

```markdown
# Task 6 - Agent 6
Update LogisticsSystem to fulfill contracts

[Specification]

Context:
- ContractSystem exists with complete_contract() method
- Trucks have current_contract_id field
- Contracts in Sim.world.contracts

Your scope: Modify sim/systems/LogisticsSystem.gd

[Detailed instructions]

---

# Task 7 - Agent 7
Create ContractCommands.gd

[Specification]

[Detailed instructions]

---

# Task 8 - Agent 8
Create ContractQueries.gd

[Specification]

[Detailed instructions]

---

# Task 9 - Agent 9
Write tests for ContractSystem

[Specification]

[Detailed instructions]
```

**Agents return:**
- Agent 6: "LogisticsSystem updated, trucks fulfill contracts, tests pass"
- Agent 7: "ContractCommands created, 2 commands, tests pass"
- Agent 8: "ContractQueries created, 2 queries, tests pass"
- Agent 9: "ContractSystem tests expanded, 10 tests total, all pass"

**Orchestrator marks Phase 4 complete.**

### Round 5: Phase 5 - UI

**Orchestrator dispatches Agent 10:**

```markdown
Create ContractUI.gd for contract management

[Specification]

Context:
- ContractCommands: create_contract(), cancel_contract()
- ContractQueries: get_contracts(), get_contract_status()

Your scope: Create scenes/ContractUI.gd

[Detailed instructions]
```

**Agent 10 returns:** "ContractUI created, integrated with commands/queries, tested manually in editor"

**Orchestrator marks Phase 5 complete.**

### Round 6: Phase 6 - Integration Tests

**Orchestrator dispatches Agent 11:**

```markdown
Write integration tests for Logistics Contracts

[Specification]

Test the full workflow end-to-end using public APIs (commands and queries).

[Detailed instructions]
```

**Agent 11 returns:** "Integration tests created, 5 scenarios, all pass"

**Orchestrator marks Phase 6 complete.**

**All implementation tasks complete. Moving to verification.**

## Phase 3: Verification Loop

### Verification Round 1

**Orchestrator dispatches verifiers in parallel (single message):**

```markdown
# Code Review Agent
Review the Logistics Contracts implementation:

[Full specification]

Git range: abc123..HEAD

Focus on:
- Architecture v2 alignment
- Command/Query separation
- Type safety
- Test coverage
- Integration with existing code

Return: Detailed code review

---

# Testing Agent
Perform exploratory testing of Logistics Contracts:

[Specification]

Test scenarios:
1. Happy path: create contract, truck fulfills, completes
2. Edge: no trucks available, contract queues
3. Edge: insufficient goods, contract fails
4. Error: invalid province ID
5. Integration: multiple contracts simultaneously

Use interact skill.

Return: Testing report
```

**Code Review Agent returns:**

```markdown
### Strengths
- Clean architecture, follows Architecture v2
- Good command/query separation
- Type safety enforced throughout
- Comprehensive unit tests

### Issues

#### Critical
None

#### Important
1. Missing test for contract cancellation
   - Location: test/sim/TestContractSystem.gd
   - Issue: No test for cancel_contract() method
   - Fix: Add test for cancellation workflow

2. Error message not helpful
   - Location: ContractCommands.gd:45
   - Issue: "Invalid input" doesn't say which field
   - Fix: Specific error messages for each validation

#### Minor
1. Could use type inference on line 78
2. Comment on line 92 is redundant

### Assessment
Ready to merge: With fixes

Important issues are easily fixed and don't affect core functionality.
```

**Testing Agent returns:**

```markdown
### Testing Summary
- Total Scenarios: 5
- Passed: 4
- Failed: 1

### Scenarios Tested
1. Happy Path - PASS
2. No Trucks Available - PASS
3. Insufficient Goods - FAIL
4. Invalid Province - PASS
5. Multiple Contracts - PASS

### Issues Found

#### Insufficient Goods Handling
- Severity: Important
- Scenario: Edge case - insufficient goods
- Expected: Contract should fail gracefully with refund
- Actual: Contract assigned, truck stuck trying to pick up goods
- Evidence: query-contracts shows status "in-progress" but truck never moves
- Reproduction: [script provided]

### Assessment
Overall: FAIL

Core functionality works but edge case not handled correctly.
```

### Analysis

**Orchestrator synthesizes findings:**

```markdown
Issues from verification:

Important (Must fix):
1. Missing cancellation test (code review)
2. Insufficient goods handling broken (testing)
3. Error messages not specific (code review)

Minor (Optional):
4. Type inference opportunity
5. Redundant comment
```

### Fix Round 1

**Orchestrator dispatches fix agent:**

```markdown
Fix these important issues:

**Issue 1: Missing cancellation test**
[Details]

**Issue 2: Insufficient goods handling**
[Details from testing report]
[Reproduction steps]

**Issue 3: Specific error messages**
[Details]

Your task:
1. Add cancellation test
2. Fix insufficient goods bug (trucks should not get stuck)
3. Improve error messages

Verify:
- Run `make test` - all tests pass
- Run `timeout 10s make run-headless` - no errors
- Manually test insufficient goods scenario

Return: Summary of fixes and verification results
```

**Fix agent returns:**

```markdown
Fixes applied:

1. Cancellation test added - passes
2. Insufficient goods bug fixed:
   - ContractSystem now validates goods availability before assignment
   - If insufficient, contract fails immediately with refund
   - Tested manually - works correctly
3. Error messages improved with specific field names

Verification:
- `make test`: All 47 tests pass
- `make run-headless`: No errors
- Manual test: Insufficient goods now fails gracefully

Files changed:
- test/sim/TestContractSystem.gd (added test)
- sim/systems/ContractSystem.gd (added validation)
- sim/commands/ContractCommands.gd (improved errors)
```

### Verification Round 2 (Targeted)

**Orchestrator does lightweight re-verification:**

```markdown
# Testing Agent
Re-test the insufficient goods scenario for Logistics Contracts:

Previously failed: Contract assigned but truck stuck

Fix applied: Validation added before assignment

Test:
1. Create contract for 100 grain with only 50 available
2. Verify contract fails immediately
3. Verify refund is processed

Return: Test result for this specific scenario
```

**Testing Agent returns:**

```markdown
### Re-Test: Insufficient Goods

Result: PASS

Behavior:
- Contract creation attempted with insufficient goods
- ContractSystem.tick() detected insufficient supply
- Contract immediately marked "failed"
- Payment refunded to faction treasury
- No truck assigned (truck remained available)

Issue resolved.
```

**Orchestrator checks code review of changed files (lightweight):**

```markdown
Review these specific fixes:

[Changed files: TestContractSystem.gd, ContractSystem.gd, ContractCommands.gd]

[Git diff of changes]

Quick check:
- Fixes look correct?
- No new issues introduced?

Return: Brief assessment
```

**Code review returns:** "Fixes look good, no new issues, validation logic is correct"

### Convergence Check

**Orchestrator evaluates:**

```markdown
Convergence Criteria:

Must-Have:
✅ All critical issues resolved (none existed)
✅ All tests passing (47 tests)
✅ Game runs without errors
✅ Code reviewer approves
✅ Tester reports functional

Should-Have:
✅ All important issues resolved (3 fixed)
✅ Test coverage adequate
✅ Architecture aligned
✅ Documentation complete

Nice-to-Have:
❌ Minor issues (type inference, comment) - deferred

Decision: CONVERGED - Ready to complete
```

## Phase 4: Final Integration

### Final Validation

**Orchestrator runs final checks:**

```bash
make test
# Output: 47 tests, 47 passed

timeout 10s make run-headless
# Output: Game runs, no errors, simulation ticks correctly

git status
# Output: 11 files changed
```

**All checks pass.**

### Documentation

**Orchestrator verifies:**
- Code comments present where needed ✅
- No docs/ updates needed for this feature ✅
- AGENTS.md needs update (new Commands and Queries) ❌

**Orchestrator updates AGENTS.md:**

```markdown
Added to AGENTS.md:

**ContractCommands:**
- create_contract(faction_id, origin_id, dest_id, goods_type, quantity, payment)
- cancel_contract(contract_id)

**ContractQueries:**
- get_contracts(faction_id) -> Array[Contract]
- get_contract_status(contract_id) -> String
```

### Summary for User

**Orchestrator provides comprehensive summary:**

```markdown
## Implementation Complete: Logistics Contracts System

### Summary
Implemented a complete logistics contracts system that allows players to create
contracts for transporting goods between provinces. Trucks bid on contracts
based on profitability and fulfill deliveries. The system integrates cleanly
with the existing logistics and economic systems.

### Changes

**Data Model:**
- Created Contract.gd (7 fields: id, origin, destination, goods type, quantity, 
  payment, status, assigned truck)
- Updated Truck.gd (added current_contract_id field)
- Updated SimWorld.gd (added contracts array and next_contract_id)

**Systems:**
- Created ContractSystem.gd (assignment, completion, failure logic)
- Updated LogisticsSystem.gd (trucks now fulfill assigned contracts)

**Commands:**
- ContractCommands.create_contract() - Create new contract
- ContractCommands.cancel_contract() - Cancel pending contract

**Queries:**
- ContractQueries.get_contracts() - Get all contracts for faction
- ContractQueries.get_contract_status() - Get status of specific contract

**UI:**
- Created ContractUI.gd (contract management panel)

**Tests:**
- 10 unit tests for Contract and ContractSystem
- 5 integration tests for full workflow
- All 47 tests passing

### Verification Results

✅ **Code Review:** Clean
- Architecture aligned with Architecture v2
- Type safety enforced
- Good separation of concerns
- Adequate test coverage
- Minor improvements deferred (type inference, comments)

✅ **Testing:** All scenarios pass
- Happy path: Contract creation → assignment → fulfillment → completion
- Edge case: No trucks available → contract queues correctly
- Edge case: Insufficient goods → contract fails gracefully with refund
- Error handling: Invalid inputs produce helpful error messages
- Integration: Multiple contracts work simultaneously

✅ **All Tests Passing:** 47/47 tests

✅ **Game Runs:** No errors in headless mode (10 second run)

### Files Changed
1. sim/data/Contract.gd (new)
2. sim/data/Truck.gd (modified)
3. sim/SimWorld.gd (modified)
4. sim/systems/ContractSystem.gd (new)
5. sim/systems/LogisticsSystem.gd (modified)
6. sim/commands/ContractCommands.gd (new)
7. sim/queries/ContractQueries.gd (new)
8. scenes/ContractUI.gd (new)
9. test/sim/TestContract.gd (new)
10. test/sim/TestContractSystem.gd (new)
11. AGENTS.md (updated)

### Next Steps
None - feature is complete and ready for use.

### Deferred Improvements
- Type inference opportunity in ContractSystem.gd:78 (minor code style)
- Redundant comment in ContractQueries.gd:92 (minor cleanup)

These can be addressed in future refactoring if desired.
```

## Session Metrics

**Planning time:** 3 agent calls (brainstorm + review)
**Implementation rounds:** 6 phases
**Verification rounds:** 2 (initial + re-verification)
**Fix rounds:** 1

**Total agent dispatches:** 14 agents
**Parallel dispatches:** 3 phases (Phases 1, 4, and verification)

**Issues found:** 3 important, 2 minor
**Issues fixed:** 3 important (100% of important issues)
**Final quality:** All must-have and should-have criteria met

**Outcome:** Feature complete, verified, and production-ready

## Key Orchestration Decisions

**Decision 1: Brainstorm approaches**
- Reason: Complex feature, not obvious best approach
- Result: Selected cleaner architecture (separate contracts)

**Decision 2: Phased implementation**
- Reason: Clear dependencies (data → logic → interface)
- Result: Each phase verified before next, caught issues early

**Decision 3: Parallel dispatch in Phases 1, 4, and verification**
- Reason: Tasks were independent within those phases
- Result: Saved time without introducing conflicts

**Decision 4: Two verification rounds**
- Reason: Important issues found in Round 1 required fixes
- Result: High confidence in quality before completion

**Decision 5: Defer minor issues**
- Reason: Diminishing returns, not worth iteration time
- Result: Pragmatic balance of quality and speed

## Lessons from This Session

**What worked well:**
- Clear planning prevented confusion
- Phased approach with dependencies explicit
- Parallel dispatch where possible
- Rigorous verification caught real issues
- Fix iteration until clean

**What could be improved:**
- Could have caught insufficient goods bug earlier with better spec
- Could have written cancellation test in initial phase

**Takeaways:**
- Brainstorming pays off for complex features
- Phased verification catches issues before they compound
- Parallel dispatch is safe with explicit dependencies
- Verification is not optional - found 3 important issues
- Iterating until clean produces high-quality results
