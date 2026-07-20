---
name: task-create
description: Create a professional Jira Task from the current work.
disable-model-invocation: false
argument-hint: "--project <PROJECT_KEY> Describe what you completed."
---

# Goal

Convert the user's completed work into a concise, professional Jira Task (never a Story).

The generated ticket must always be created as a **Task** issue type in Jira, not as a Story. It should be clear, minimal, and follow software engineering best practices. The amount of detail should always be proportional to the user's input.

---

# User Input

The command format:

```text
/task-create --project <PROJECT_KEY>

<description of completed work>
```

Example:

```text
/task-create --project PROJ

1. Implemented login authentication.
2. Added JWT refresh tokens.
3. Added unit tests.
```

Extract the project key from the `--project` argument.

Treat everything after the command as the work description.

If `--project` is missing, prompt the user for the project key before creating the Jira issue.

---

# Configuration & Environment Variables

Read environment variables from `.env` (looking in the current skill folder, `.agents/skills/`, `skills/`, or root workspace) or system environment variables.

Environment Variables Used:
- `JIRA_DEVELOPER_NAME` (Developer Name)
- `JIRA_DEVELOPER_EMAIL` (Developer Email)
- `JIRA_DEVELOPER_ACCOUNT_ID` (Jira Account ID)
- `JIRA_ALLOWED_PROJECTS` (Comma-separated allowed project keys, e.g., `PROJ,APP`)

Defaults:
- Issue Type: `Task`
- Priority: `Medium`
- Labels: `ai-generated`

Never hardcode developer credentials.

---

# Validate the Project

Before generating the Jira issue:

1. Read `JIRA_ALLOWED_PROJECTS` from `.env` (e.g. `PROJ,APP`).
2. Verify that the project key supplied through `--project` exists in that allowed list.
3. If not found, stop immediately and respond:

   > Invalid project key '<PROJECT_KEY>'. Allowed project keys are: <JIRA_ALLOWED_PROJECTS>.

4. Do **not** call Jira APIs to validate the project. Continue only if the project key is in `JIRA_ALLOWED_PROJECTS`.

---

# Generate the Jira Issue

Generate the following fields:

## Summary
- Maximum 80 characters.
- Use imperative style (e.g. "Add Navigation Bar", "Implement Login Authentication").

## Description
- Keep the description extremely concise.
- Generate **only** a plain numbered list (1, 2, 3...) of actions completed.
- Limit to 1–5 concise numbered points.
- Do **not** add extra sections, headings, or HTML formatting.

## Priority
- Default: `Medium`. Only increase if user explicitly indicates urgency.

## Assignee
- Assign to `JIRA_DEVELOPER_ACCOUNT_ID`.

---

# Create the Jira Issue

Use the Jira tools exposed by the connected **Atlassian Rovo MCP Server**.

Create a Jira issue with:
- Project Key: value from `--project`
- Issue Type: `Task` (must be exactly "Task", never create a "Story")
- Summary
- Description
- Labels: `ai-generated`
- Priority: `Medium`
- Assignee: `JIRA_DEVELOPER_ACCOUNT_ID`

If assigning during creation is not supported, create the issue first and then assign it to `JIRA_DEVELOPER_ACCOUNT_ID`.

Never invent issue keys or URLs. Only report values returned by the Jira MCP tools.

---

# Response

If successful, return:

**Task Created**

- Issue Key
- Project Key
- Summary
- Assignee
- Priority
- Link

If creation fails:
- Explain the actual error returned by Jira.
