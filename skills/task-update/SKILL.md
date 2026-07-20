---
name: task-update
description: Update the status of a Jira task or bug.
disable-model-invocation: false
argument-hint: "<ISSUE_KEY> <TARGET_STATUS> (e.g. PROJ-123 done)"
---

# Goal

Update/move the status of an existing Jira issue (Task or Bug) to a valid workflow status defined in environment variables.

---

# User Input

The command format:

```text
/task-update <ISSUE_KEY> <TARGET_STATUS>
```

Examples:

```text
/task-update PROJ-123 done
/task-update APP-456 in-progress
/task-update PROJ-789 in-review
/task-update PROJ-101 todo
```

Extract `ISSUE_KEY` (e.g. `PROJ-123`) and `TARGET_STATUS` (e.g. `Done`, `In Progress`, `In Review`, `To Do`).

If either argument is missing, prompt the user for the missing details before updating.

---

# Configuration & Environment Variables

Read environment variables from `.env` (looking in the current skill folder, `.agents/skills/`, `skills/`, or root workspace) or system environment variables.

Environment Variables Used:
- `JIRA_ALLOWED_PROJECTS` (Comma-separated allowed project keys, e.g. `PROJ,APP`)
- `JIRA_ALLOWED_STATUSES` (Comma-separated allowed statuses, default: `To Do,In Progress,In Review,Done`)

Default Allowed Statuses (if `JIRA_ALLOWED_STATUSES` is not set):
- `To Do`
- `In Progress`
- `In Review`
- `Done`

Status Alias Mappings:
- `todo`, `backlog` -> `To Do`
- `wip`, `inprogress`, `in-progress` -> `In Progress`
- `review`, `inreview`, `in-review` -> `In Review`
- `done`, `closed`, `resolved` -> `Done`

---

# Validate Project and Target Status

## 1. Validate Project Key
1. Extract project key prefix from `<ISSUE_KEY>` (e.g., prefix for `PROJ-123` is `PROJ`).
2. Check against `JIRA_ALLOWED_PROJECTS` from `.env`.
3. If invalid, stop immediately and respond:
   > Invalid project key '<PROJECT_KEY>'. Allowed project keys are: <JIRA_ALLOWED_PROJECTS>.

## 2. Validate Target Status
1. Match `<TARGET_STATUS>` against `JIRA_ALLOWED_STATUSES` or the alias mappings (case-insensitive).
2. If mapped to a valid status, use that exact standard status name.
3. If invalid, stop immediately and respond:
   > Invalid status '<TARGET_STATUS>'. Allowed statuses are: To Do, In Progress, In Review, Done.

---

# Update the Jira Issue Status

Use Jira tools exposed by **Atlassian Rovo MCP Server**.

1. Retrieve available workflow transitions for `<ISSUE_KEY>`.
2. Find the transition corresponding to the validated `TARGET_STATUS`.
3. Execute the transition on `<ISSUE_KEY>`.

Never invent issue keys or status names.

---

# Response

If successful, return:

**Status Updated**

- Issue Key
- Project Key
- Previous Status
- New Status
- Link

If the update fails:
- Explain the actual error returned by Jira.
