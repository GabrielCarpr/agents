---
name: jira-cli
description: Use when a Jira ticket ID (e.g. BOARD-123) is referenced, or when needing to view, context-check, or update the status of Jira issues.
---

# Jira CLI

## Overview
This skill governs the interaction with the Jira CLI tool to manage tickets, providing context and status updates. It ensures agents automatically fetch ticket details when IDs are mentioned and handle status transitions correctly.

## When to Use
- **Ticket ID Reference:** ANY time a pattern like `[A-Z]+-\d+` (e.g., `RPL-123`, `PROJ-456`) appears in the conversation or task description.
- **Context Gathering:** When you need to know the requirements, parent story, epic, or linked dependencies of a task.
- **Status Updates:** When instructed to "start" a task, "mark as in progress", or otherwise update the ticket state.

## Core Commands

### 1. View Issue & Context
**Primary Command:**
```bash
jira issue view <ISSUE_KEY>
```

**Output Analysis:**
When reading the output, specifically look for:
- **Summary & Description:** The core task requirements.
- **Type:** `Subtask`, `Story`, `Bug`, `Epic`.
- **Parent:** If `Type` is `Subtask`, identifying the `Parent` key is CRITICAL for context.
- **Links/Epic:** Any `Epic Link` or `Issue Links` (blockers/dependencies).

### 2. Update Status (Move/Transition)
**Primary Command:**
```bash
jira issue move <ISSUE_KEY> "In Progress"
```

**Handling Transition Names:**
Project workflows vary. Common status names include `"In Progress"`, `"In Development"`, `"Selected for Development"`.
- If `"In Progress"` fails, the error message often lists available transitions. Use the one that matches "starting work".

## Usage Protocol

### Automatic Context Fetching
**Rule:** If a ticket ID is referenced, you must understand it before acting.

1.  **Detect ID:** User says "Can you look at RPL-123?" or "Fix bug RPL-123".
2.  **Fetch:** Run `jira issue view RPL-123`.
3.  **Analyze:**
    *   *Is it a subtask?* -> Run `jira issue view <PARENT_KEY>` to get the full story.
    *   *Is it blocked?* -> Note dependencies.
4.  **Action:** Proceed with the task using the gathered context.

### Viewing Sibling Subtasks
To see other subtasks under the same parent (useful for understanding the broader scope):
```bash
jira issue list -q "parent = <PARENT_KEY>"
```

### Viewing Epic Context
To see all issues in the related Epic:
```bash
jira issue list -q "epic = <EPIC_KEY>"
```

## Common Mistakes
- **Missing context of epics, siblings, parents**: Always get all available context from siblings, parents, epics, other subtasks etc.
- **Acting on a Subtask in Isolation:** Implementing a subtask without checking the parent Story's description often leads to missing requirements. **Always check the parent.**
- **Guessing Status Names:** If `jira issue move ... "In Progress"` fails, do not guess blindly. Read the error message or check current status.
- **Ignoring Linked Issues:** Failing to notice a "Blocked by" link and attempting to work on a blocked ticket.
