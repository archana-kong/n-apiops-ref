# 🚀 Deploy Kong Configuration with GitHub Actions

This repository automates the deployment of Kong Gateway configurations to [Kong Konnect](https://konnect.konghq.com) using [decK](https://docs.konghq.com/deck/) and GitHub Actions.

---

## 📋 Workflow Overview

This workflow allows you to:

- Validate and render Kong configurations
- Merge per-service API configurations with global configs
- Lint with custom rulesets
- Detect changes with `deck diff`
- Automatically sync or rollback configuration changes to Konnect

---

## 🎮 Triggering the Workflow

Manually run the workflow via **workflow_dispatch**.

### Inputs Required:
- `environment`: Choose from `dev`, `staging`, `production` (optional)
- `konnect_control_plane_name`: Name of the Konnect control plane (mandatory)

---

## 🧱 Folder Structure

```bash
.github/workflows/
├── deploy-kong.yaml                # This GitHub Actions workflow
apis/
├── flights/                        # Service1
│   └── kong/
│       ├── plugins/                #Service1 specific Plugin folder
│       ├── patches/                #Service1 specific Patches folder
│       └── kong.yaml               #Service1 kong configuration file
|── routes/                         # Service2
│   └── kong/
│       ├── plugins/
│       ├── patches/
│       └── kong.yaml
global/                             # Global congiguration
└── kong/
    ├── merges/                     # Merge files folder
    └── kong.ruleset.global.yaml    # Linting rules after merge
common/                             # Common configuration folder
└── kong/
    └── plugins/                    # Common Plugins
    └── kong.ruleset.api.yaml       # Common Linting rules
```

---

## 🛠 Jobs in the Workflow

### 🔧 build-apis
- Validates and updates API-specific Kong configurations using plugins, patches, and tags.
- Uploads updated kong.yaml per service.

### 🧩 build-config
- Downloads all API artifacts and merges them with global rules.
- Validates and diffs with the current deployed config in Konnect.
- Uploads diff output and merged config.

### 🚀 apply-config
- If a change is detected, backs up current config, applies new one via `deck sync`.
- Automatically rolls back if sync fails.
- Archives the latest backup.

---

## 🧪 decK Commands Used

- `deck file validate`: Validate local kong.yaml
- `deck file add-plugins`: Inject plugins from files
- `deck file patch`: Apply patches to config
- `deck file add-tags`: Add metadata tags
- `deck file render`: Evaluate environment variables
- `deck file lint`: Lint using custom rulesets
- `deck gateway dump`: Backup current config from Konnect
- `deck gateway diff`: Detect differences between local and remote
- `deck gateway sync`: Apply configuration

---

## 🧠 Best Practices

- Use versioned `deck` via `kong/setup-deck`
- Maintain clear separation between global and service-specific configs
- Always lint and validate before syncing
- Use rollback strategy to protect production
- Tag configs (`generated_by:deck`) for traceability
- You can use the env inputs under Tags if required.

---

## 🔐 Secrets Required

Ensure these are set in GitHub Secrets:
- `KONNECT_TOKEN`: Konnect Personal Access Token

---

## 📎 Reference
- [Kong decK Docs](https://docs.konghq.com/deck/)

