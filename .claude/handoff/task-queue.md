# Task Queue

<!-- PM writes tasks here. CC picks them up with msg -->
<!-- Other projects can also add tasks here for n8n automation requests -->

## TASK-001 🔵 NEW
**Priority:** HIGH
**Created:** 2025-12-26
**Status:** NEW

### Description
Initial setup - inventory all existing workflows and export to this repo.

### Your Tasks

**Part 1: Inventory Existing Workflows**

Use MCP to list workflows on both instances:
```
n8n-casaos:list_workflows
n8n-mac:list_workflows
```

Document what exists, their status (active/inactive), and purpose.

**Part 2: Export All Workflows**

For each workflow, get the full details and save to appropriate location:
- CasaOS workflows → `workflows/backup/casaos/workflow-name.json`
- Mac workflows → `workflows/backup/mac/workflow-name.json`

Use `n8n-casaos:get_workflow` and `n8n-mac:get_workflow` with `verbosity: full`.

**Part 3: Create Initial Documentation**

For each workflow, create a basic doc in `workflows/docs/workflow-name.md`:
- Name and ID
- Instance (CasaOS/Mac)
- Status (active/inactive)
- Trigger type
- Brief description of what it does
- Any visible integrations/credentials used

**Part 4: Update CLAUDE.md Inventory**

Update the "Current Workflows Inventory" section in CLAUDE.md with what you find.

### Acceptance Criteria
- [ ] All workflows inventoried from both instances
- [ ] JSON exports saved to workflows/backup/
- [ ] Documentation created for each workflow
- [ ] CLAUDE.md inventory section updated

---

## TASK-002 🔵 NEW (after TASK-001)
**Priority:** MEDIUM
**Created:** 2025-12-26
**Status:** NEW

### Description
Create a GitHub Workflow Backup workflow that automatically exports all n8n workflows to this repo on a schedule.

### Your Tasks

**Part 1: Design the Workflow**

Create a workflow that:
1. Triggers on schedule (daily at 3 AM)
2. Uses n8n API to list all workflows
3. For each workflow, exports the JSON
4. Commits changes to homelab-n8n repo
5. Only commits if there are changes

**Part 2: Build on CasaOS Instance**

This should run on CasaOS since it's the production instance.

**Part 3: Document**

Create `workflows/docs/github-workflow-backup.md` with full documentation.

### Notes
- Will need GitHub credential in n8n
- Consider using the GitHub node or HTTP Request with PAT
- Should backup its own workflow too!

### Acceptance Criteria
- [ ] Workflow created and active on CasaOS
- [ ] Successfully backs up workflows to this repo
- [ ] Documentation complete
- [ ] JSON export of the backup workflow itself saved

---

*No more pending tasks*
