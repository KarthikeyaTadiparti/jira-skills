---
name: publish-actions
description: Convert recorded actions in actions.md into Jira tickets for a project key.
disable-model-invocation: false
argument-hint: "--project <PROJECT_KEY>"
---

# Goal

Batch process all pending recorded actions from `actions.md`, push corresponding Jira tickets (Tasks or Bugs) under the specified project key, and reset/empty `actions.md`.

---

# User Input

The command format:

```text
/publish-actions --project <PROJECT_KEY>
```

Example:

```text
/publish-actions --project PROJ
```

Extract the project key from the `--project` argument.

If `--project` is missing, prompt the user for the project key before proceeding.

---

# Configuration & Environment Variables

Read environment variables from `.env` (looking in current skill folder, `.agents/skills/`, `skills/`, or root workspace) or system environment variables.

Environment Variables Used:
- `JIRA_DEVELOPER_NAME` (Developer Name)
- `JIRA_DEVELOPER_EMAIL` (Developer Email)
- `JIRA_DEVELOPER_ACCOUNT_ID` (Jira Account ID)
- `JIRA_ALLOWED_PROJECTS` (Comma-separated allowed project keys, e.g. `PROJ,APP`)

Defaults:
- Issue Type Task: `Task`
- Issue Type Bug: `Bug`
- Task Priority: `Medium`
- Bug Priority: `High`
- Labels: `ai-generated`

Do **not** query Jira to validate project keys ahead of time.

---

# Validate the Project Key

1. Read `JIRA_ALLOWED_PROJECTS` from `.env`.
2. Verify that the project key supplied via `--project` exists in `JIRA_ALLOWED_PROJECTS`.
3. If not found, stop immediately and respond:
   > Invalid project key '<PROJECT_KEY>'. Allowed project keys are: <JIRA_ALLOWED_PROJECTS>.

---

# Read, Publish, and Clear `actions.md`

1. Read `actions.md` from `.agents/skills/actions.md` or `skills/actions.md`.
2. If `actions.md` does not exist or has no pending items (`- [ ]`), respond:
   > No pending actions found in `actions.md`. Use `/record-actions` to start recording session actions first.

3. For each pending item marked with `- [ ]`:
   - Parse whether the item is a **Task** or a **Bug**.
   - Extract the **Summary** and **Description**.
   - Use Jira tools exposed by **Atlassian Rovo MCP Server** to create the ticket:
     - **Project Key**: value supplied in `--project`
     - **Issue Type**: "Task" if Task, "Bug" if Bug
     - **Summary**: Action summary (max 80 chars)
     - **Description**: Action description
     - **Assignee**: `JIRA_DEVELOPER_ACCOUNT_ID` (for Tasks) or unassigned (for Bugs)
     - **Labels**: `ai-generated`
     - **Priority**: `Medium` (for Tasks) or `High` (for Bugs)
   - Capture the created Jira Issue Key (e.g. `PROJ-101`) and URL link.

4. **Empty `actions.md`**:
   - Once all tickets are successfully created, overwrite `actions.md` to reset it to an empty template state:
     ```markdown
     # Recorded Actions

     Pending actions to be published to Jira via `/publish-actions`:
     ```

---

# Response Format

If tickets are created successfully:

**Published Actions to Jira**

- **[PROJ-101](https://jira.atlassian.net/browse/PROJ-101)**: Implemented login authentication API
- **[PROJ-102](https://jira.atlassian.net/browse/PROJ-102)**: Fixed null pointer crash on submit button

> All actions have been published to Jira and `actions.md` has been cleared.

If any ticket creation fails:
- Report the specific error for that item and keep remaining unpublished items in `actions.md`.
