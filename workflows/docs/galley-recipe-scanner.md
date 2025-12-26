# Galley Recipe Scanner

**ID:** jhz0aTSMaqnbHddS
**Instance:** CasaOS
**Status:** Active
**Category:** Data

## Purpose

Scans recipe images/PDFs from Google Drive, extracts recipe data using Claude AI, and commits structured JSON to the galley-meal-planner repo.

## Trigger

- **Schedule:** Daily at 6:00 AM
- **Webhook:** POST to `/webhook/scanner-trigger`

## Flow

1. List files in Google Drive "Scanned Recipes" folder
2. Filter to only files (not folders)
3. Download each file
4. Detect if PDF or image
5. Send to Claude API for extraction
6. Parse response into structured recipe JSON
7. Check extraction confidence
8. If high/medium confidence:
   - Upload scan to Galley app
   - Push JSON to GitHub `pending-recipes/`
   - Move file to "Processed" folder
9. If low confidence:
   - Push to `failed-recipes/`
   - Move to "Failed" folder

## Integrations

- **Google Drive:** Service Account (read/move files)
- **Anthropic API:** Claude claude-sonnet-4-20250514 for vision extraction
- **GitHub:** Push to galley-meal-planner repo
- **Galley App:** HTTP upload to 192.168.20.16:3122

## Credentials

- Google Service Account account
- Anthropic account
- GitHub - Workflow Backup

## Notes

- Uses 5-second delay between Claude API calls to avoid rate limits
- Extracts page numbers for multi-page PDFs
- Flags recipes requiring oven (for sailboat galley use)
