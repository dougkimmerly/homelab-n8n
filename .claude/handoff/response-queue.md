# Response Queue

<!-- CC writes responses here. PM archives them after review -->

## TASK-001 Response
**Completed:** 2025-12-26

### Summary
Inventoried and exported all 9 workflows from both n8n instances.

### CasaOS (4 workflows)
- Galley Recipe Scanner
- Workflow Backup to GitHub - CasaOS
- Galley Scanner Status
- Music Library Nightly Maintenance

### Mac (5 workflows)
- Workflow Backup to GitHub - Mac
- via check for new dates
- via Get new dates Reverse
- Check missing fares
- via get fares reverse

### Deliverables
- [x] JSON exports in `workflows/backup/casaos/` (4 files)
- [x] JSON exports in `workflows/backup/mac/` (5 files)
- [x] Documentation in `workflows/docs/` (5 doc files)
- [x] CLAUDE.md inventory section updated

### Notes
- All workflows are currently Active
- Existing backup workflows modified to target this repo

---

## TASK-002 Response
**Status:** IN PROGRESS

### Issue
Backup workflow paths are reversed:
- CasaOS workflow pushing to `mac/` folder
- Mac workflow pushing to `casaos/` folder

### Fix Required
Swap the paths in each workflow's "Process Workflows" node.

---
