# Puzzle Version Comparison

This NestJS module compares records from the existing `puzzle_versions` table.
Import `PuzzleVersionComparisonModule` into a NestJS application that already
configures TypeORM and the puzzle version entity.

Endpoints:

- `GET /puzzle-version-comparison/compare?fromVersionId=<id>&toVersionId=<id>`
  compares two versions belonging to the same puzzle.
- `GET /puzzle-version-comparison/puzzles/<puzzleId>/history` returns versions
  in chronological (ascending version) order.

The comparison response contains JSON-pointer content changes and dedicated
changes for `testCases`, `difficulty`, and `hints` (or the legacy singular
`hint` field).

## Maintenance mode guard

`src/maintenance-mode/maintenance.guard.ts` reads the existing maintenance
service status and blocks non-admin requests with HTTP 503. Admin roles and
routes marked with the existing maintenance decorators bypass the check.
`/health` and `/auth/*` are excluded by default; additional exact paths or
prefix wildcards can be supplied through `MAINTENANCE_GUARD_OPTIONS` or the
maintenance service's `allowedRoutes`. Scheduled start/end timestamps are
checked directly, so enforcement does not wait for the service's scheduled
maintenance cron job.
