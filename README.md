# Jira AI Agent Skills Suite

A suite of 5 custom skills for AI coding agents to create, update, record, and publish Jira tickets directly from your workspace.

---

## ⚡ Quick Install via `skills` CLI

Anyone can install all skills in this repository into their workspace using the `skills` CLI:

```bash
npx skills@latest add KarthikeyaTadiparti/jira-skills
```

---

## 📁 Repository Directory Structure

```text
.
├── README.md                          # Main project & setup documentation
└── skills/                            # Root skills directory for CLI installer
    ├── .env.example                   # Secret & project settings template
    ├── task-create/
    │   └── SKILL.md                   # Instruction rules for /task-create
    ├── task-update/
    │   └── SKILL.md                   # Instruction rules for /task-update
    ├── report-bug/
    │   └── SKILL.md                   # Instruction rules for /report-bug
    ├── record-actions/
    │   └── SKILL.md                   # Instruction rules for /record-actions
    └── publish-actions/
        └── SKILL.md                   # Instruction rules for /publish-actions
```

---

## 🛠️ Setup & Configuration (`.env`)

Copy `skills/.env.example` to `.env` (in your workspace or skill folder) and set your details:

```env
# Jira Developer Credentials
JIRA_DEVELOPER_NAME="Your Name"
JIRA_DEVELOPER_EMAIL="your.email@example.com"
JIRA_DEVELOPER_ACCOUNT_ID="your_jira_account_id"

# Allowed Jira Project Keys (comma-separated)
JIRA_ALLOWED_PROJECTS="PROJ,APP"
```

---

## 🚀 Skills Reference & Commands

| Command | Description | Syntax |
| :--- | :--- | :--- |
| **`/task-create`** | Creates a concise Jira **Task** ticket from completed work. | `/task-create --project <KEY> <description>` |
| **`/task-update`** | Moves/updates the workflow status of an issue. | `/task-update <ISSUE_KEY> <STATUS>` |
| **`/report-bug`** | Creates a detailed Jira **Bug** report ticket. | `/report-bug --project <KEY> <bug details>` |
| **`/record-actions`** | Toggles automatic session logging of actions to `actions.md`. | `/record-actions [stop\|status]` |
| **`/publish-actions`** | Batch creates Jira tickets for all logged actions & resets `actions.md`. | `/publish-actions --project <KEY>` |

---

### 1. `/task-create` Command

Converts completed work into a concise, professional Jira **Task** ticket.

```text
/task-create --project PROJ

1. Create responsive navigation bar.
2. Add application logo and title.
3. Include primary navigation links.
```

---

### 2. `/task-update` Command

Updates the status of an existing Jira issue (Task or Bug) to a valid workflow status defined directly in the skill (`todo`, `in-progress`, `in-review`, `done`).

```text
/task-update PROJ-101 in-progress
/task-update PROJ-101 done
```

---

### 3. `/report-bug` Command

Converts a bug description into a structured Jira **Bug** ticket.

```text
/report-bug --project PROJ The login page is crashing on mobile Safari when clicking submit.
```

---

### 4. `/record-actions` Command

Toggles **Recording Mode** for your AI session. Once enabled, every completed request in the conversation is automatically logged to `actions.md`.

```text
/record-actions          # Starts recording session actions
/record-actions status   # Checks recording status & pending items
/record-actions stop     # Stops recording
```

---

### 5. `/publish-actions` Command

Reads all pending logged actions from `actions.md`, creates corresponding Jira Task/Bug tickets, and resets `actions.md`.

```text
/publish-actions --project PROJ
```

---

## 🛡️ Security Best Practices

- Ensure `.env` files are included in `.gitignore`.
- Never commit Jira API tokens or personal account IDs to public repositories.
