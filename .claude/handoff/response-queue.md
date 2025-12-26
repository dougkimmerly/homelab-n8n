# Response Queue

<!-- CC writes responses here. PM archives them after review -->

## RESPONSE: TASK-001 🟡 BLOCKED
**Date:** 2025-12-26
**Status:** BLOCKED - Configuration Required

### Issue
The n8n MCP tools cannot access workflows. Both instances return 404 errors.

### Diagnosis
Both n8n instances are running (healthz returns OK), but the **Public API** is not enabled:

| Endpoint | Mac Result | CasaOS Result |
|----------|------------|---------------|
| `/healthz` | OK | OK |
| `/rest/workflows` | 401 Unauthorized | 401 Unauthorized |
| `/api/v1/workflows` | 404 not found | 404 not found |

The MCP tools use the Public API (`/api/v1/*`), which is disabled by default.

### Action Required

**For each n8n instance (Mac + CasaOS):**

1. Open n8n web UI
2. Go to **Settings** → **API**
3. Toggle **Enable Public API** = ON
4. Click **Create API Key** and save it
5. Update the MCP server config with the new API key

**MCP config location:** `~/.claude.json` or Claude Code settings

Example MCP config:
```json
{
  "mcpServers": {
    "n8n-mac": {
      "command": "npx",
      "args": ["-y", "@leonardsellem/n8n-mcp-server"],
      "env": {
        "N8N_HOST": "http://localhost:5678",
        "N8N_API_KEY": "<your-mac-api-key>"
      }
    },
    "n8n-casaos": {
      "command": "npx",
      "args": ["-y", "@leonardsellem/n8n-mcp-server"],
      "env": {
        "N8N_HOST": "http://192.168.20.19:5678",
        "N8N_API_KEY": "<your-casaos-api-key>"
      }
    }
  }
}
```

### Next Steps
Once configured, reply with `msg` and I'll continue with the inventory task.

---

*No other pending responses*
