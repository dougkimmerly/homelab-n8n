# n8n Expert Skill

Deep knowledge for building and managing n8n workflows.

## MCP Tools Available

### Workflow Management
- `list_workflows` - List all workflows (use verbosity: concise or full)
- `get_workflow` - Get specific workflow by ID
- `create_workflow` - Create new workflow
- `update_workflow` - Update existing workflow
- `delete_workflow` - Delete workflow (use with caution)
- `activate_workflow` - Enable workflow
- `deactivate_workflow` - Disable workflow

### Execution Management
- `list_executions` - List workflow executions (filter by status, workflow)
- `get_execution` - Get execution details (includeData: true for full debug)

### Node Discovery
- `list_available_nodes` - See what nodes are available in the instance

## Workflow JSON Structure

```json
{
  "name": "[Category] Workflow Name",
  "nodes": [
    {
      "id": "unique-id",
      "name": "Node Name",
      "type": "n8n-nodes-base.nodeType",
      "typeVersion": 1,
      "position": [x, y],
      "parameters": { }
    }
  ],
  "connections": {
    "Node Name": {
      "main": [
        [
          { "node": "Next Node", "type": "main", "index": 0 }
        ]
      ]
    }
  },
  "settings": {
    "executionOrder": "v1"
  }
}
```

## Common Node Types

### Triggers
- `n8n-nodes-base.scheduleTrigger` - Cron/interval trigger
- `n8n-nodes-base.webhook` - HTTP webhook trigger
- `n8n-nodes-base.manualTrigger` - Manual execution

### Core Nodes
- `n8n-nodes-base.httpRequest` - Make HTTP calls
- `n8n-nodes-base.if` - Conditional branching
- `n8n-nodes-base.set` - Set/transform data
- `n8n-nodes-base.code` - JavaScript/Python code
- `n8n-nodes-base.merge` - Merge data streams
- `n8n-nodes-base.splitInBatches` - Process in batches

### Integrations
- `n8n-nodes-base.github` - GitHub operations
- `n8n-nodes-base.googleSheets` - Google Sheets
- `n8n-nodes-base.slack` - Slack messaging
- `n8n-nodes-base.pushover` - Push notifications

## Schedule Trigger Patterns

```json
// Every 5 minutes
{
  "rule": { "interval": [{ "field": "minutes", "minutesInterval": 5 }] }
}

// Daily at 3 AM
{
  "rule": { "interval": [{ "field": "hours", "triggerAtHour": 3 }] }
}

// Cron expression
{
  "rule": { "cronExpression": "0 3 * * *" }
}
```

## Error Handling

### Try/Catch Pattern
1. Enable "Continue On Fail" on risky nodes
2. Check `$json.error` in subsequent nodes
3. Branch to error handler if needed

### Error Trigger
Create a separate workflow with Error Trigger to catch failures from other workflows.

## Best Practices

1. **Name nodes descriptively** - "Fetch User Data" not "HTTP Request"
2. **Use Set nodes for data shape** - Document what data looks like between steps
3. **Add sticky notes** - Explain complex logic
4. **Test incrementally** - Execute node by node during development
5. **Handle errors explicitly** - Don't let workflows fail silently
6. **Use environment variables** - For URLs, keys that might change
7. **Keep workflows focused** - One job per workflow, use sub-workflows

## Debugging

1. Use `list_executions` with `status: error` to find failures
2. Use `get_execution` with `includeData: true` for full trace
3. Check execution data at each node to find where things went wrong
4. Look for null/undefined values in the data flow
