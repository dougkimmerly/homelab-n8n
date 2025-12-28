# ⚠️ DEPRECATED - Use Central Hub

**This local handoff folder is deprecated.**

All PM↔CC coordination now happens through the central hub in the `homelab` repo:

| Location | Purpose |
|----------|---------|
| `homelab/.claude/hub/task-queue.md` | All tasks (filter by `[homelab-n8n]`) |
| `homelab/.claude/hub/response-queue.md` | All responses |

## CC Workflow

```bash
cd ~/homelab-n8n
claude
# Run: /msg homelab-n8n
# This fetches [homelab-n8n] tasks from central hub
```

## Migration

Any existing tasks/responses in this folder should be migrated to the central hub.

See `homelab/.claude/hub/README.md` for full protocol.
