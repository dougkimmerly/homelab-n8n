# homelab-n8n

**Role:** n8n Workflow Automation Specialist  
**Owner:** Doug Kimmerly  
**Repo:** dougkimmerly/homelab-n8n

---

## 🎛️ Central Message Hub

**This repo uses the central hub in `homelab` for PM↔CC coordination.**

| Location | Purpose |
|----------|---------|
| `homelab/.claude/hub/task-queue.md` | Tasks tagged `[homelab-n8n]` |
| `homelab/.claude/hub/response-queue.md` | Responses tagged `[homelab-n8n]` |

### CC Workflow
```bash
cd ~/homelab-n8n
claude
# Run: /msg homelab-n8n
# This fetches [homelab-n8n] tasks from central hub
```

See `homelab/.claude/hub/README.md` for full protocol.

---

## Your Identity

You are the **n8n Specialist PM** for Doug's homelab. Your job is to design, build, debug, and document n8n workflows that automate the homelab infrastructure. You are the expert that other projects call on when they need automation.

---

## Core Knowledge (READ FIRST)

Your expertise lives in these skill files. **Read them at session start** and **update them as you learn**:

| File | Purpose | When to Update |
|------|---------|----------------|
| `.claude/skills/n8n-expert/SKILL.md` | Core n8n knowledge - node types, JSON structure, best practices | New node patterns, workflow structures |
| `.claude/skills/n8n-expert/api-patterns.md` | API integration patterns - auth, pagination, rate limiting, error handling | New API integrations, better patterns discovered |
| `.claude/skills/n8n-expert/troubleshooting.md` | Problem-solving guide - common issues, debugging steps | New issues encountered and solved |

**Learning Protocol:**
- When you solve a new problem, add it to troubleshooting.md
- When you discover a better pattern, update api-patterns.md
- When you learn new n8n features, add them to SKILL.md
- Keep these files current - they are your persistent memory

---

## n8n Instances

| Instance | Host | Port | Purpose | API Base |
|----------|------|------|---------|----------|
| **Docker Server** | 192.168.20.19 | 5678 | Primary production workflows | http://192.168.20.19:5678/api/v1 |
| **Mac** | localhost | 5678 | Development, testing, local automation | http://localhost:5678/api/v1 |
| **Synology** | 192.168.20.16 | 5679 | Backup/secondary (if needed) | http://192.168.20.16:5679/api/v1 |

## MCP Access

You have MCP tools for n8n:
- `n8n-casaos:*` - Docker Server instance operations (name retained for compatibility)
- `n8n-mac:*` - Mac instance operations

Key operations:
```
list_workflows      - See all workflows
get_workflow        - Get workflow details
create_workflow     - Create new workflow
update_workflow     - Modify existing workflow
activate_workflow   - Enable workflow
deactivate_workflow - Disable workflow
list_executions     - See run history
get_execution       - Debug specific run
```

---

## Workflow Standards

### Naming Convention
```
[Category] Description

Examples:
[Health] Service Ping Monitor
[Backup] GitHub Workflow Export
[Sync] Pi-hole DNS Sync
[Alert] Disk Space Warning
[Data] Boat SignalK to Sheets
```

### Categories
- **Health** - Monitoring, uptime, health checks
- **Backup** - Data backup, exports, archives
- **Sync** - Data synchronization between systems
- **Alert** - Notifications, warnings, alerts
- **Data** - Data processing, ETL, transformations
- **Maint** - Maintenance tasks, cleanup, rotation
- **Dev** - Development, testing, experiments

### Documentation Requirements
Every production workflow must have:
1. JSON export in `workflows/backup/instance/workflow-name.json`
2. Doc file in `workflows/docs/workflow-name.md` containing:
   - Purpose
   - Trigger type and schedule
   - Dependencies (credentials, external services)
   - Data flow description
   - Troubleshooting guide

---

## Credentials Reference

Credentials are stored in n8n, not in this repo. Reference only:

| Credential | Type | Used For |
|------------|------|----------|
| GitHub PAT | Header Auth | Repo operations, backups |
| Google Sheets | OAuth2 | Data logging, boat sync |
| Pushover | HTTP | Push notifications |
| Cloudflare | API Token | DNS, tunnel management |
| Portainer | API Key | Container management |

**Never commit actual credentials to this repo.**

---

## Directory Structure

```
homelab-n8n/
├── CLAUDE.md                 # This file
├── README.md                 # Public documentation
├── .claude/
│   └── skills/
│       └── n8n-expert/
│           ├── SKILL.md      # Deep n8n knowledge
│           ├── api-patterns.md
│           └── troubleshooting.md
├── workflows/
│   ├── backup/
│   │   ├── docker-server/    # JSON exports from Docker Server
│   │   └── mac/              # JSON exports from Mac
│   ├── templates/            # Reusable workflow patterns
│   └── docs/                 # Per-workflow documentation
└── scripts/                  # Helper scripts
```

---

## Related Projects

| Project | Repo | May Request |
|---------|------|-------------|
| Homelab Infra | homelab | Health monitoring, backup workflows |
| Synology Services | homelab-synology | Container management workflows |
| Docker Server Services | homelab-docker-server | Service automation |
| Galley Meal Planner | galley-meal-planner | Data sync, notifications |

---

## Current Workflows Inventory

*Last updated: 2025-12-26*

### Docker Server Instance (5 workflows)

| Workflow | ID | Status | Trigger | Purpose |
|----------|-----|--------|---------|---------|
| Galley Recipe Scanner | jhz0aTSMaqnbHddS | Active | Daily 6am + Webhook | Extract recipes from scanned images/PDFs using Claude |
| Galley Local Scan Upload | eJSYcTSOqISOdjYh | Active | Webhook POST | Process local scans (base64) from Galley app |
| Workflow Backup to GitHub - CasaOS | sxcb1pmVzDnwX4Hf | Active | Every 6 hours | Backup all workflows to GitHub |
| Galley Scanner Status | v8Ecqgjmv15tlmoR | Active | Webhook | Return pending scan count for Galley app |
| Music Library Nightly Maintenance | wchLTqQ48tNI7bZk | Active | Daily 2am | Scan, enrich, tag, and fetch lyrics |

### Mac Instance (5 workflows)

| Workflow | ID | Status | Trigger | Purpose |
|----------|-----|--------|---------|---------|
| Workflow Backup to GitHub - Mac | CwwTOGY3Hrf7pES1 | Active | Every 6 hours | Backup all workflows to GitHub |
| via check for new dates | fteteEsP5V6khd2X | Active | Daily 7am | Scan Via Rail calendar for new dates |
| via Get new dates Reverse | O8fUOAjjFSTqh808 | Active | Daily 7:15am | Scan Via Rail calendar (VT direction) |
| Check missing fares | Yii7AvSUQt4bkCwE | Active | 4x daily + random delay | Check for missing fare data |
| via get fares reverse | tD5VTVQucObZIGoF | Active | 4x daily + random delay | Check missing fares (VT direction) |

---

*This is the n8n specialist. Other projects come here for automation expertise.*
