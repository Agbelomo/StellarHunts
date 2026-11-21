# Migration Validator

Static safety checks for database migrations, used to block unsafe schema
changes in CI. Lives in `stellar-extensions/scripts/` and has **no runtime
dependencies** — Node.js 18+ is enough.

## Usage

```bash
node stellar-extensions/scripts/migration-validator.js --path backend/src/content/migrations
node stellar-extensions/scripts/migration-validator.js --path backend/src/content/migrations --json
node stellar-extensions/scripts/migration-validator.js --path backend/src/content/migrations --fail-on warning
```

| Option | Meaning |
| --- | --- |
| `-p, --path <dir>` | Directory to scan (repeatable) |
| `--fail-on <level>` | `error` (default), `warning`, or `never` |
| `--json` | Machine-readable report on stdout |
| `--quiet` | Suppress the human-readable report |
| `-h, --help` | Show usage |
| `--version` | Show version |

Exit codes: `0` clean, `1` blocking findings, `2` invalid usage or unreadable
path.

## Checks

| Check | Severity |
| --- | --- |
| `DROP TABLE` / `DROP DATABASE` / `DROP SCHEMA` | error |
| `TRUNCATE` | error |
| `DELETE FROM` without `WHERE` | error |
| `DROP COLUMN` / `dropColumn()` | warning |
| `DROP INDEX` / `CONSTRAINT` / `VIEW` / `TYPE` / `SEQUENCE` | warning |
| Missing `down()` or empty `down()` (TypeORM) | error |
| `*.up.sql` without a matching `*.down.sql` | error |
| Standalone `.sql` without a `-- +migrate Down` section | error |
| File name not `<timestamp>[-_]<description>.(ts\|js\|sql)` | error |

Forward migrations are checked for destruction; `down()` methods and
`.down.sql` files are rollback code and are only checked for emptiness. Keywords
inside comments or string literals are ignored.

### Intentional destruction

Add a comment containing `migration-validator: allow-destructive` to a file
that legitimately removes data (for example, a one-off cleanup migration):

```ts
// migration-validator: allow-destructive -- stale table removed on purpose
```

The suppression applies to destructive-operation and data-loss findings in that
file only.

## CI

Because the default `--fail-on error` exits non-zero, the validator can gate a
pipeline directly:

```yaml
- name: Validate migrations
  run: |
    node stellar-extensions/scripts/migration-validator.js \
      --path backend/src/content/migrations \
      --path backend/src/achievements/migrations
```

## Tests

```bash
node --test stellar-extensions/scripts/migration-validator.test.js
```

## Known limitations

- Heuristic parser, not a full SQL/TypeScript AST: unusual cross-statement DDL
  or dynamic identifiers assembled at runtime can be missed.
- Only `up()` is inspected for destructive operations, so an unsafe statement
  hidden in an unusual helper method is not detected.
- Naming validation assumes timestamp-prefixed files (TypeORM / common SQL
  migration conventions).
