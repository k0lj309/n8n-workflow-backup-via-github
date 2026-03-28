# n8n Workflow Backup

Backs up all n8n workflows to this repo automatically. Workflows are organized into folders by tag and named after the workflow itself. Every run creates or updates the relevant files — GitHub handles the version history.

```
Manual ──► Config ──► Get All ──► Prepare ──► Loop Over Items
                      Workflows    File Paths       │
                      (n8n API)    (Code node)      │ loop
                                                    ▼
                                             Get File from GitHub
                                                (HTTP GET)
                                                    │
                                                    ▼
                                             Is New or Exists?
                                                (sha check)
                                                    │
                                                    ▼
                                              New or Exists?
                                               (If node)
                                              │         │
                                           true        false
                                              │         │
                                              ▼         ▼
                                          Create     Edit Existing
                                          New File      File
                                          (PUT)      (PUT + sha)
                                              │         │
                                              └────┬────┘
                                                   │
                                                   ↩ back to loop
```


