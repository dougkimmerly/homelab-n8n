# Workflow Backup to GitHub

**IDs:**
- CasaOS: sxcb1pmVzDnwX4Hf
- Mac: CwwTOGY3Hrf7pES1

**Instances:** CasaOS, Mac
**Status:** Active
**Category:** Backup

## Purpose

Automatically exports all n8n workflows to GitHub for version control and backup.

## Trigger

- **Schedule:** Every 6 hours

## Flow

1. Call n8n API to list all workflows
2. For each workflow:
   - Clean credential IDs (keep names only)
   - Convert to JSON and base64 encode
   - Generate safe filename
3. Upload to GitHub repo
4. If file exists (422 error):
   - Get current file SHA
   - Update with new content
5. Update README with backup summary

## Target Repository

`dougkimmerly/homelab-n8n-workflows`

## Integrations

- **n8n API:** Local instance API
- **GitHub API:** File commits

## Credentials

- n8n API (local)
- GitHub account / GitHub - Workflow Backup

## Notes

- Excludes itself from backup to avoid recursion
- Strips credential IDs for security (keeps names for reference)
- Both CasaOS and Mac instances run independently
