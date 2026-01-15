# homelab-n8n

n8n workflow automation for Doug's homelab infrastructure.

## Overview

This repository manages n8n workflows that automate various homelab tasks including:
- Service health monitoring
- Data synchronization
- Backup automation
- Alert notifications
- Container management

## n8n Instances

| Instance | Location | Purpose |
|----------|----------|---------|
| Docker Server | 192.168.20.19:5678 | Production workflows |
| Mac | localhost:5678 | Development and testing |
| Synology | 192.168.20.16:5679 | Secondary/backup |

## Structure

```
├── workflows/
│   ├── backup/            # JSON exports of all workflows
│   │   ├── docker-server/ # Docker Server instance workflows
│   │   └── mac/           # Mac instance workflows
│   ├── templates/         # Reusable workflow patterns
│   └── docs/              # Per-workflow documentation
├── scripts/               # Helper scripts
└── .claude/               # Claude Code integration
```

## Workflow Categories

- **[Health]** - Monitoring, uptime, health checks
- **[Backup]** - Data backup, exports, archives
- **[Sync]** - Data synchronization between systems
- **[Alert]** - Notifications, warnings
- **[Data]** - Data processing, ETL
- **[Maint]** - Maintenance tasks, cleanup

## Cross-Project Requests

Other homelab projects can request n8n workflows by adding tasks to `.claude/handoff/task-queue.md`.

## Related

- [homelab](https://github.com/dougkimmerly/homelab) - Main documentation
- [homelab-synology](https://github.com/dougkimmerly/homelab-synology) - Synology services
- [homelab-docker-server](https://github.com/dougkimmerly/homelab-docker-server) - Docker Server services
