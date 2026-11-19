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
