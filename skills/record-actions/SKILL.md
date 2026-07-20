---
name: record-actions
description: Start or stop recording completed actions/tasks into actions.md file.
disable-model-invocation: false
argument-hint: "[stop|status]"
---

# Goal

Enable or manage session action recording into `actions.md`.

**Key Behavior**:
- The user calls `/record-actions` ONCE at the start or during a conversation.
- Calling `/record-actions` turns ON **Recording Mode**.
- From that moment onwards, for EVERY subsequent message/task completed in the conversation, the agent MUST automatically log the completed action into `actions.md` without requiring the user to call `/record-actions` again.
- Calling `/record-actions stop` turns OFF **Recording Mode**.

---

# User Input Syntax

```text
/record-actions
/record-actions stop
/record-actions status
```

Examples:
- `/record-actions` -> Activates action recording mode for the rest of the conversation.
- `/record-actions stop` -> Deactivates action recording mode.
- `/record-actions status` -> Checks whether recording mode is currently active and lists pending items in `actions.md`.

---

# File Location & Storage

The target file is `actions.md` located inside `.agents/skills/actions.md` or `skills/actions.md`.

If `actions.md` does not exist when recording starts, create it with the following header:

```markdown
# Recorded Actions

Pending actions to be published to Jira via `/publish-actions`:

```

---

# Entry Format in `actions.md`

Each recorded action MUST be appended as an uncompleted markdown checkbox item (`- [ ]`):

```markdown
- [ ] **[Task|Bug]**: <Short Summary>
  - Description: <Detailed explanation of what was completed>
  - Date: <YYYY-MM-DD>
```

Rules:
- Infer whether the completed work is a **Task** (feature addition, refactoring, docs) or a **Bug** (fix, patch, crash resolution).
- Write a concise summary (max 80 chars).
- Detail what work was accomplished.
- Always use `- [ ]` so `/publish-actions` can process it as pending.

---

# Persistent Recording Instructions for the Agent

When **Recording Mode is ACTIVE**:
1. After completing ANY task, code change, or action requested by the user in subsequent turns, automatically read `actions.md` and append the new recorded entry.
2. Confirm to the user at the end of your response that the action has been logged into `actions.md`.

---

# Response Formats

## When Starting Recording (`/record-actions`):
> **Recording Mode Activated**
> From now on, all completed actions in this conversation will automatically be recorded to `actions.md`.
> Run `/publish-actions --project <PROJECT_KEY>` whenever you are ready to create Jira tickets for recorded actions.

## When Stopping Recording (`/record-actions stop`):
> **Recording Mode Deactivated**
> Logged actions in `actions.md` are saved. Use `/publish-actions --project <PROJECT_KEY>` to convert them into Jira tickets.

## When Checking Status (`/record-actions status`):
> **Recording Mode**: [Active / Inactive]
> **Pending Recorded Actions**: <Number of pending items in actions.md>
