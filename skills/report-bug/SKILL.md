---
name: report-bug
description: Convert a bug description into a professional Jira Bug ticket.
disable-model-invocation: false
argument-hint: "--project <PROJECT_KEY> Describe the bug."
---

# Goal

Convert the user's bug description into a professional Jira Bug ticket.

Generate a well-structured report suitable for engineering teams.

---

# User Input

The command format:

```text
/report-bug --project <PROJECT_KEY>

<description of the bug>
```

Example:

```text
/report-bug --project PROJ

The login page is crashing on mobile Safari when clicking submit.
```

Extract the project key from the `--project` argument.

Treat everything after the command as the bug description.

If `--project` is missing, ask the user for the project key before creating the Jira issue.

---

# Configuration & Environment Variables

Read environment variables from `.env` (looking in current skill folder, `.agents/skills/`, `skills/`, or root workspace) or system environment variables.

Environment Variables Used:
- `JIRA_DEVELOPER_NAME` (Developer Name)
- `JIRA_DEVELOPER_EMAIL` (Developer Email)
- `JIRA_ALLOWED_PROJECTS` (Comma-separated allowed project keys, e.g. `PROJ,APP`)

Defaults:
- Issue Type: `Bug`
- Priority: `High`
- Assignee: Unassigned
- Labels: `ai-generated`

Never hardcode developer credentials.

---

# Validate the Project

Before generating the Jira issue:

1. Read `JIRA_ALLOWED_PROJECTS` from `.env`.
2. Verify that the project key supplied through `--project` exists in that list.
3. If not found, stop immediately and respond:

   > Invalid project key '<PROJECT_KEY>'. Allowed project keys are: <JIRA_ALLOWED_PROJECTS>.

4. Do **not** call Jira APIs to validate the project. Continue only if the project key is in `JIRA_ALLOWED_PROJECTS`.

---

# Generate the Jira Bug

Generate the following fields:

## Summary
- Maximum 80 characters. Clearly describe the issue.

## Description
Use standard sections:
- **Problem**: Explanation of the issue.
- **Steps to Reproduce**: Numbered steps from description.
- **Expected Result**: Expected behavior.
- **Actual Result**: Observed behavior.
- **Environment**: Inferred environment (Development, QA, Production, or Unknown).

## Severity & Priority
- Severity: Infer Critical, High, Medium, or Low.
- Priority: Default `High`.

## Labels
- Always include `ai-generated`.

## Assignment
- Do **not** assign the created bug to anyone. Leave unassigned.

---

# Create the Jira Issue

Use the Jira tools exposed by the connected **Atlassian Rovo MCP Server**.

Create a Bug using:
- Project Key: value supplied in `--project`
- Issue Type: `Bug`
- Summary
- Description
- Labels: `ai-generated`
- Priority: `High`
- Assignee: Leave unassigned

Never invent issue keys or URLs.

---

# Response

If successful, return:

**Bug Created**

- Issue Key
- Project Key
- Summary
- Severity
- Priority
- Assignee (Unassigned)
- Link

If creation fails:
- Explain the actual error returned by Jira.
