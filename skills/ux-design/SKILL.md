---
name: ux-design
description: Create wireframes through user interviews using wiremd.dev
---

## What I do

I help design user interfaces through a structured, interview-driven process:

1. **Interview you** to understand UX requirements, goals, and constraints using the Question tool
2. **Create wireframes** using wiremd.dev syntax to visualize the solution
3. **Iterate on designs** based on your feedback

I produce text-based, low-fidelity wireframes that focus on layout, information architecture, and user flow rather than visual design details.

## When to use me

Use this skill when:
- You need to design a UI, screen, or interface
- You want to visualize a feature or workflow before implementation
- You need to clarify UX requirements through structured discussion
- You want to create wireframes or mockups
- A new feature requires UI design planning
- You're asking questions like "design a..." or "how should the UI for..."

## How I work

### Phase 1: Requirements Interview

I'll explore the important documentation of the project to understand it. There are located within `./docs`. `./backlog` may also be useful to explain what work has been completed.

I will use the **Question tool** to conduct a structured interview. I'll ask about:

1. **The problem:**
   - What problem is this UI solving?
   - Who are the users?
   - What is the primary goal or action users need to complete?

2. **Scope:**
   - Is this a single screen, a flow, or an entire feature?
   - Are there existing UI patterns or components to follow?
   - What platform/context (desktop, mobile, web, game UI)?

3. **Constraints:**
   - Are there technical limitations?
   - Screen size or layout constraints?
   - Performance considerations?

4. **Content requirements:**
   - What information needs to be displayed?
   - What actions need to be available?
   - What is the priority/hierarchy of information?

5. **Context:**
   - When/where will users access this?
   - What task are they trying to accomplish?
   - What comes before/after this screen in the user journey?

### Phase 2: Design Wireframes

Once requirements are clear, I'll create multiple wireframe concepts using **wiremd.dev** syntax:

1. **Reference wiremd.dev** using the WebFetch tool if needed:
   - URL: https://wiremd.dev/

2. **Create wireframes** using ASCII-art style layouts:
   - Boxes for containers: `[ Content ]`
   - Buttons: `( Button )`
   - Input fields: `[_________]`
   - Use spacing and alignment to show hierarchy
   - Add arrows `->` to show flow or relationships

3. **Structure the output:**
   ```markdown
   # [Screen/Feature Name]
   
   ## Overview
   [Brief description of what this screen does]
   
   ## Wireframe
   
   [ASCII-art wireframe using wiremd.dev patterns]
   
   ## Interactions
   - [Describe key interactions, states, edge cases]
   
   ## Notes
   - [Design rationale, alternatives considered, open questions]
   ```

### Phase 3: Iterate

After presenting the wireframe options, I will:

1. **Explain design decisions:**
   - Why I organized information this way
   - What user needs this addresses
   - What trade-offs I made

2. **Ask for feedback:**
   - Does this solve the problem?
   - Is anything missing or unclear?
   - Would you like to explore alternatives?

3. **Refine based on feedback:**
   - Update wireframes as needed
   - Consider multiple variations if there's uncertainty
   - Document changes and rationale

## Example output

For a request like "I need a UI for managing standing orders", I would:

1. First interview to understand:
   - User goals (create, view, edit, delete standing orders?)
   - Required information (what data makes up a standing order?)
   - Context (where in the app does this live?)
   - Constraints (existing UI patterns to follow?)

2. Then create the wireframes like:

```
# Standing Orders Management UI

## Overview
Allows players to view, create, edit, and delete automated trading orders.

## Wireframe

+--------------------------------------------------+
|  Standing Orders                        ( + New ) |
+--------------------------------------------------+
|                                                  |
|  [ Active Orders ]              [ History ]      |
|                                                  |
|  +--------------------------------------------+  |
|  | Grain -> Province A            ( Edit )    |  |
|  | Buy when price < $50           ( Delete )  |  |
|  | Max quantity: 100t                         |  |
|  | Status: Active ●                           |  |
|  +--------------------------------------------+  |
|                                                  |
|  +--------------------------------------------+  |
|  | Steel -> ITA Trade Post        ( Edit )    |  |
|  | Sell when price > $200         ( Delete )  |  |
|  | Max quantity: 50t                          |  |
|  | Status: Paused ○                           |  |
|  +--------------------------------------------+  |
|                                                  |
+--------------------------------------------------+

## Interactions
- Click "+ New" to open standing order creation dialog
- Click "Edit" to modify existing order
- Click "Delete" to remove order (with confirmation)
- Click order card to expand details
- Toggle Active/Paused status by clicking status indicator

## Notes
- Card-based list for scannability
- Inline actions for quick access
- Status indicator for at-a-glance monitoring
- Consider adding filters/search for large order lists
```

3. Then ask for favourite so far

4. Save that to a file (so it can be previewed) in `./docs/ux`

5. Ask for more feedback and iterate on it and produce more variants


## Best practices I follow

1. **Always interview first** - Never assume requirements
2. **Keep wireframes simple** - Focus on structure, not aesthetics
3. **Annotate interactions** - Wireframes alone don't explain behavior
4. **Consider edge cases** - Empty states, errors, loading states
5. **Show, don't tell** - Use actual wireframe syntax, not just descriptions
6. **Iterate quickly** - Get feedback early and often
7. **Document rationale** - Explain why I made design decisions
8. **Present multiple concepts** - We can then converge on one and iterate on it. Each concept should be substantially different

## Common patterns I consider

- **List views:** Cards, tables, or simple lists?
- **Forms:** Single-column, multi-column, wizard flow?
- **Actions:** Inline, toolbar, context menu, or floating action button?
- **Navigation:** Tabs, sidebar, breadcrumbs, or modal dialogs?
- **Feedback:** Toast notifications, inline messages, or modal confirmations?
- **Empty states:** What does the user see when there's no data?
- **Loading states:** How do you indicate async operations?

## Tools I use

- **Question tool** - To interview you interactively. REMEMBER, each questopn label must be less than 30 characters long
- **WebFetch tool** - To reference wiremd.dev documentation
- **No code modifications** - I only create wireframe documentation for you to review, and then save the final artifact to a file

## Expected output

I will always provide:
1. A complete wireframe using wiremd.dev patterns
2. Annotations explaining interactions and behavior
3. Design rationale and trade-offs
4. Open questions or alternatives to consider
5. An offer to iterate or explore other options
