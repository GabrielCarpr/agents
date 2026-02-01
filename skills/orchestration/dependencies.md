# Dependency Management Strategies

This guide explains how to manage dependencies when orchestrating parallel and sequential work.

## Understanding Dependencies

**Independent tasks:** Can be worked on simultaneously without interference
**Dependent tasks:** One must complete before another can start
**Partially dependent:** Some aspects can overlap, some must sequence

## Identifying Dependencies

### Data Dependencies

**Example:**
```
Task A: Create BuildingData class
Task B: Create BuildingSystem (uses BuildingData)
```

**Dependency:** B depends on A (data structure must exist before system can use it)

**Strategy:** Sequential - complete A, then start B

### Logical Dependencies

**Example:**
```
Task A: Implement production system
Task B: Implement consumption system
Task C: Implement market system (matches producers and consumers)
```

**Dependency:** C depends on both A and B

**Strategy:** 
1. A and B in parallel
2. Wait for both to complete
3. Then start C

### No Dependencies

**Example:**
```
Task A: Implement Farm building
Task B: Implement Mine building
Task C: Implement Factory building
```

**Dependency:** None - buildings are independent

**Strategy:** Fully parallel - all three agents work simultaneously

## Dependency Patterns

### Pattern 1: Linear Chain

```
A → B → C → D
```

**When:** Each task builds on the previous

**Example:**
1. Data model
2. System logic using the model
3. Commands using the system
4. UI using the commands

**Strategy:**
```markdown
Agent 1: Implement A
[wait]
Agent 2: Implement B (uses A)
[wait]
Agent 3: Implement C (uses B)
[wait]
Agent 4: Implement D (uses C)
```

**Pros:** Clear, simple, no conflicts
**Cons:** Slow - no parallelism

### Pattern 2: Parallel with Convergence

```
A ─┐
B ─┤→ D
C ─┘
```

**When:** Multiple independent tasks that feed into a final integration

**Example:**
1. Three data models (parallel)
2. Integration system (uses all three)

**Strategy:**
```markdown
# Phase 1: Parallel
Task 1: Implement A
Task 2: Implement B
Task 3: Implement C
[All in single message - parallel execution]

[Wait for all three]

# Phase 2: Integration
Task 4: Implement D (uses A, B, C)
```

**Pros:** Maximum parallelism, then integration
**Cons:** Integration might find issues requiring rework

### Pattern 3: Layered

```
    A   B   C
     \ | /
      \|/
       D
      / \
     E   F
```

**When:** Clear architectural layers (data → logic → interface)

**Example:**
1. Data layer (multiple entities in parallel)
2. Logic layer (systems using the data)
3. Interface layer (commands/queries/UI)

**Strategy:**
```markdown
# Layer 1: Data (parallel)
Task 1: Entity A
Task 2: Entity B
Task 3: Entity C

[Wait for layer 1]

# Layer 2: Logic (parallel within layer)
Task 4: System D (uses A, B, C)

[Wait for layer 2]

# Layer 3: Interface (parallel)
Task 5: Commands E
Task 6: Queries F
```

**Pros:** Clean separation, some parallelism within layers
**Cons:** More phases means more coordination

### Pattern 4: Independent Streams

```
A → B → C
D → E → F
G → H → I
```

**When:** Multiple features being implemented independently

**Example:**
- Feature 1: New resource type (data → system → interface)
- Feature 2: New building type (data → system → interface)
- Feature 3: New mechanic (data → system → interface)

**Strategy:**
```markdown
# Three parallel streams, each with internal sequence
Stream 1:
  Task 1: Implement A
  [wait]
  Task 2: Implement B (uses A)
  [wait]
  Task 3: Implement C (uses B)

Stream 2:
  Task 4: Implement D
  [wait]
  Task 5: Implement E (uses D)
  [wait]
  Task 6: Implement F (uses E)

Stream 3:
  Task 7: Implement G
  [wait]
  Task 8: Implement H (uses G)
  [wait]
  Task 9: Implement I (uses H)

# All three streams run in parallel
# Within each stream, tasks are sequential
```

**Pros:** Maximum parallelism across features
**Cons:** High coordination complexity, potential conflicts at integration

## Creating Dependency Graphs

### Step 1: List All Tasks

Write down every task that needs to be done:
- Create Contract data class
- Create Truck data class
- Update SimWorld to hold contracts
- Implement ContractSystem
- Update LogisticsSystem to use contracts
- Create ContractCommands
- Create ContractQueries
- Create UI for contract management
- Write tests for Contract
- Write tests for ContractSystem
- Write integration tests

### Step 2: Identify Dependencies

For each task, ask: "What must exist before this can be done?"

**Example analysis:**

```
Contract data class
  ← depends on: nothing
  
Truck data class
  ← depends on: nothing
  
Update SimWorld
  ← depends on: Contract class, Truck class
  
ContractSystem
  ← depends on: Contract class, Truck class, SimWorld updated
  
LogisticsSystem update
  ← depends on: Contract class, ContractSystem
  
ContractCommands
  ← depends on: ContractSystem
  
ContractQueries
  ← depends on: ContractSystem
  
UI
  ← depends on: ContractCommands, ContractQueries
  
Tests for Contract
  ← depends on: Contract class
  
Tests for ContractSystem
  ← depends on: ContractSystem
  
Integration tests
  ← depends on: Everything
```

### Step 3: Draw the Graph

```
Layer 1 (Parallel):
  Contract class
  Truck class

Layer 2 (Sequential):
  SimWorld update

Layer 3 (Sequential):
  ContractSystem

Layer 4 (Parallel):
  LogisticsSystem update
  ContractCommands
  ContractQueries
  Tests for ContractSystem

Layer 5 (Sequential):
  UI

Layer 6 (Sequential):
  Integration tests
```

### Step 4: Optimize for Parallelism

Look for opportunities to overlap:

**Can we start UI while LogisticsSystem is being updated?**
- No - UI might need LogisticsSystem changes

**Can we write tests while implementation is happening?**
- Yes - tests for Contract can happen as soon as Contract exists
- Tests for ContractSystem can happen as soon as ContractSystem exists

**Optimized:**

```
Layer 1 (Parallel):
  A: Contract class
  B: Truck class

Layer 2 (Sequential):
  C: SimWorld update (needs A, B)

Layer 3 (Sequential):
  D: ContractSystem (needs A, B, C)

Layer 4 (Parallel):
  E: LogisticsSystem update (needs D)
  F: ContractCommands (needs D)
  G: ContractQueries (needs D)
  H: Tests for Contract (needs A)
  I: Tests for ContractSystem (needs D)

Layer 5 (Sequential):
  J: UI (needs F, G)

Layer 6 (Sequential):
  K: Integration tests (needs everything)
```

**Note:** Tests for Contract (H) can actually start in Layer 1 or 2, not Layer 4.

**Further optimized:**

```
Phase 1 (Parallel):
  Task 1: Contract class
  Task 2: Truck class
  Task 3: Tests for Contract (can start immediately)

Phase 2:
  Task 4: SimWorld update

Phase 3:
  Task 5: ContractSystem

Phase 4 (Parallel):
  Task 6: LogisticsSystem update
  Task 7: ContractCommands
  Task 8: ContractQueries
  Task 9: Tests for ContractSystem

Phase 5:
  Task 10: UI

Phase 6:
  Task 11: Integration tests
```

## Practical Tips

### Tip 1: When in Doubt, Be Conservative

If you're not sure whether tasks can run in parallel, sequence them.

**Better:** Slower but safe
**Worse:** Fast but broken due to conflicts

### Tip 2: Data → Logic → Interface

This pattern works for most features:
1. Create data structures (parallel if multiple)
2. Implement systems/logic (parallel if multiple, sequential after data)
3. Create commands/queries (parallel after logic)
4. Build UI (after commands/queries)
5. Write tests (throughout, as each piece completes)

### Tip 3: Test Early and Often

Don't wait until the end to test. Test each layer as it completes:
- Data layer done? Test data classes
- Logic layer done? Test systems
- Interface done? Test commands/queries
- UI done? Test UI integration

**Benefits:**
- Catch issues early
- Verify assumptions before building on them
- Reduce risk of late-stage failures

### Tip 4: Explicit Handoffs

When task B depends on task A, give task B's agent explicit context:

```markdown
Implement ContractSystem.

**Context:**
Task A (completed) implemented Contract class with these fields:
- contract_id: int
- goods_type: String
- quantity: int
- origin_province_id: int
- destination_province_id: int
- status: String

**Your task:**
Build ContractSystem that uses this Contract class...
```

This reduces the chance of agent B misunderstanding agent A's work.

### Tip 5: Version Control Helps

If parallel agents might conflict:
- Have each agent work in a separate branch
- Merge sequentially after review
- Resolve conflicts carefully

Or:
- Have clear file ownership (Agent A owns file X, Agent B owns file Y)
- Minimal overlap = minimal conflicts

## Example: Complex Feature Breakdown

**Feature:** Logistics Contract System

**Tasks identified:**
1. Contract data class
2. Truck data class updates
3. SimWorld integration
4. ContractSystem (generates and assigns contracts)
5. LogisticsSystem updates (trucks fulfill contracts)
6. ContractCommands (player creates contracts)
7. ContractQueries (player views contracts)
8. UI for contract management
9. Tests (unit and integration)

**Dependency analysis:**

```
         1          2
     (Contract) (Truck)
         |      /
         |     /
         |    /
         |   /
          \ /
           3
      (SimWorld)
           |
           4
    (ContractSystem)
       /   |   \
      /    |    \
     5     6     7
  (Logistics) (Cmds) (Queries)
      \    |    /
       \   |   /
        \  |  /
          \|/
           8
         (UI)
           |
           9
        (Tests)
```

**Execution plan:**

```markdown
# Phase 1 (Parallel)
Task 1: Implement Contract.gd data class
Task 2: Update Truck.gd with contract_id field
Task 3: Write tests for Contract.gd

# Phase 2 (Sequential)
Task 4: Update SimWorld.gd to hold contracts array

# Phase 3 (Sequential)
Task 5: Implement ContractSystem.gd

# Phase 4 (Parallel)
Task 6: Update LogisticsSystem.gd to fulfill contracts
Task 7: Create ContractCommands.gd
Task 8: Create ContractQueries.gd
Task 9: Write tests for ContractSystem.gd

# Phase 5 (Sequential)
Task 10: Create ContractUI.gd

# Phase 6 (Integration)
Task 11: Write integration tests
Task 12: Verify full workflow
```

**Rationale:**
- Phase 1: Data foundations (parallel)
- Phase 2: World integration (sequential after data)
- Phase 3: Core logic (sequential after world)
- Phase 4: Interface and extensions (parallel after core)
- Phase 5: UI (sequential after interface)
- Phase 6: Final verification

**Total phases:** 6
**Total parallelism opportunities:** 2 phases have parallel work
**Critical path:** 1 → 3 → 4 → 5 → 6 → 10 → 11 (7 sequential steps)

## Handling Unexpected Dependencies

**Problem:** You dispatched tasks in parallel, but discover a dependency

**Example:**
- Agent A is implementing ContractCommands
- Agent B is implementing ContractQueries
- You thought they were independent
- Agent A discovers they need a helper function that B is also writing

**Solutions:**

1. **Stop and re-sequence:**
   - Pause Agent B
   - Let Agent A finish
   - Resume Agent B with context from Agent A's work

2. **Coordination layer:**
   - Create a shared utility module
   - Have both agents use it
   - Implement the utility first, then resume both

3. **Duplicate and merge:**
   - Let both agents finish independently
   - Manually merge the duplicate work
   - Not ideal, but sometimes fastest

**Best practice:** Discover dependencies during planning, not execution.

## Tools for Dependency Management

### TodoWrite for Tracking

```markdown
TodoWrite:
- [ ] Phase 1a: Contract data class (Agent 1)
- [ ] Phase 1b: Truck updates (Agent 2)
- [ ] Phase 1c: Contract tests (Agent 3)
- [ ] Phase 2: SimWorld integration (blocked by 1a, 1b)
- [ ] Phase 3: ContractSystem (blocked by 2)
- ...
```

Mark tasks as:
- pending: Not started
- in_progress: Agent working on it
- completed: Done and verified

### Dependency Comments

When creating tasks, add dependency comments:

```markdown
Task 5: Implement ContractSystem
  Dependencies: 
    - Contract.gd (Task 1) ✓
    - Truck.gd updated (Task 2) ✓
    - SimWorld updated (Task 4) ✓
  Blocks:
    - LogisticsSystem update (Task 6)
    - ContractCommands (Task 7)
    - ContractQueries (Task 8)
```

This makes dependencies explicit and tracks status.

## Summary

**Key principles:**
1. Identify dependencies early (during planning)
2. Create explicit dependency graphs
3. Maximize parallelism within constraints
4. Use layered pattern for architectural clarity
5. Test each layer before building the next
6. Track dependencies in TodoWrite
7. When in doubt, be conservative (sequence rather than parallelize)

**Remember:** The goal is fast, correct implementation. Parallelism is good, but conflicts and rework are expensive. Balance speed with safety.
