# Music Library Nightly Maintenance

**ID:** wchLTqQ48tNI7bZk
**Instance:** CasaOS
**Status:** Active
**Category:** Maint

## Purpose

Runs nightly maintenance on the music library: scanning new tracks, enriching artist data, tagging, and fetching lyrics.

## Trigger

- **Schedule:** Daily at 2:00 AM (cron: `0 2 * * *`)

## Flow

1. Health check API endpoint
2. Start maintenance job with limits:
   - lyricsLimit: 100
   - enrichLimit: 50
3. Poll job status every 60 seconds
4. Max polling: 2 hours (120 iterations)
5. On complete/error/timeout:
   - Format results summary
   - Build final summary message

## Target Service

Music Library API at `192.168.20.16:3847`

## Endpoints Used

- `GET /api/health` - Health check
- `POST /api/actions/maintenance` - Start job
- `GET /api/jobs/{jobId}` - Poll status

## Integrations

- **Music Library API:** HTTP requests

## Credentials

None (internal network)

## Notes

- Long-running job with async polling pattern
- 30-second timeout on individual requests
- Outputs metrics: new tracks, enriched artists, tagged tracks, lyrics found
