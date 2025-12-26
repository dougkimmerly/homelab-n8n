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

## TASK-002 ✅ COMPLETE (Pre-existing)
**Priority:** MEDIUM
**Created:** 2025-12-26
**Completed:** 2025-12-26 (already existed)
**Status:** COMPLETE

### Description
GitHub Workflow Backup - automatically exports all n8n workflows to this repo on a schedule.

### Notes
This workflow was already built and running before this project was created. Just needs to be:
1. Documented in `workflows/docs/`
2. JSON exported to `workflows/backup/casaos/`

This will happen as part of TASK-001.

---

*No more pending tasks*
