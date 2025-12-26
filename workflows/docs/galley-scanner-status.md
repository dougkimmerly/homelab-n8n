# Galley Scanner Status

**ID:** v8Ecqgjmv15tlmoR
**Instance:** CasaOS
**Status:** Active
**Category:** Health

## Purpose

Webhook endpoint that returns the current count and list of pending recipe scans in Google Drive.

## Trigger

- **Webhook:** GET/POST to `/webhook/scanner-status`

## Flow

1. Receive webhook request
2. Query Google Drive for files in "Scanned Recipes" folder
3. Format response with count and file names
4. Return JSON response

## Response Format

```json
{
  "count": 3,
  "files": ["recipe1.pdf", "recipe2.jpg", "recipe3.png"]
}
```

## Integrations

- **Google Drive:** Service Account (list files)

## Credentials

- Google Service Account account

## Notes

- Called by Galley app to show pending scan count
- Used for status dashboard
