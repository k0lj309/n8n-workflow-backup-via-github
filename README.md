# <img src="https://api.iconify.design/mdi/github.svg?color=%23FF6D5A" width="32" valign="middle"> Workflow Backup to GitHub — n8n Workflow

Automatically backs up all your n8n workflows to a GitHub repository. Workflows are organized into folders by their first tag and named after the workflow itself. Every run creates or updates the relevant files — GitHub handles the full version history.

## <img src="https://api.iconify.design/mdi/lightning-bolt.svg?color=%23FF6D5A" width="20" valign="middle"> How it works

The workflow fetches all workflows from your n8n instance via the n8n API, builds a safe file path for each one (using tags as folders), then loops through them. For each workflow it checks if the file already exists in GitHub — if it does, it updates it with the latest content; if not, it creates it fresh.

```
Manual Trigger → Config → Get All Workflows → Prepare File Paths → Loop Over Items
                                                                          │
                                                                    (for each workflow)
                                                                          │
                                                                          ▼
                                                                Get File from GitHub
                                                                    (sha check)
                                                                          │
                                                                          ▼
                                                                   New or Exists?
                                                                  │             │
                                                                new           exists
                                                                  │             │
                                                                  ▼             ▼
                                                            Create File    Update File
                                                                  │             │
                                                                  └──── loop ───┘
```

## <img src="https://api.iconify.design/mdi/folder-open.svg?color=%23FF6D5A" width="20" valign="middle"> Output structure

Workflows are organized by their first tag. Untagged workflows go into a `workflows/` fallback folder.

```
your-repo/
├── workflows/
│   └── My_Untagged_Workflow.json
├── Github/
│   └── Backup_Workflows_to_GitHub.json
└── Automation/
    └── Daily_Report.json
```

## <img src="https://api.iconify.design/mdi/wrench.svg?color=%23FF6D5A" width="20" valign="middle"> Setup

### Prerequisites

- A running n8n instance (self-hosted or n8n Cloud)
- A GitHub repository to back up into
- A GitHub Personal Access Token with `repo` scope
- An n8n API key

### <img src="https://api.iconify.design/mdi/import.svg?color=%23FF6D5A" width="18" valign="middle"> 1. Import the workflow

Import `n8n-github-backup.json` into your n8n instance:

- Via UI: **Settings → Import Workflow**
- Via CLI: `n8n import:workflow --input=n8n-github-backup.json`

### <img src="https://api.iconify.design/mdi/key.svg?color=%23FF6D5A" width="18" valign="middle"> 2. Add credentials

You'll need two credentials in n8n:

| Credential | Type | Used for |
|---|---|---|
| n8n API | n8n API | Fetching all workflows from your instance |
| GitHub | GitHub API | Reading and writing files to your repo |

### <img src="https://api.iconify.design/mdi/cog.svg?color=%23FF6D5A" width="18" valign="middle"> 3. Configure the Config node

Open the workflow and edit the **Config** node — set your values:

| Field | Description |
|---|---|
| `repo_owner` | Your GitHub username or organization |
| `repo_name` | The repository to back up into |

### <img src="https://api.iconify.design/mdi/play-circle.svg?color=%23FF6D5A" width="18" valign="middle"> 4. Run it

Trigger the workflow manually. It will loop through all your workflows and push them to GitHub. Run it on a schedule to keep backups up to date.

## <img src="https://api.iconify.design/mdi/information.svg?color=%23FF6D5A" width="20" valign="middle"> Notes

- Archived workflows are automatically skipped
- Workflow names are sanitized for safe file paths (spaces → underscores, special chars removed)
- Each commit message includes the workflow name and whether it was created or updated
- The SHA check ensures only changed files get committed — no unnecessary noise in your git history
