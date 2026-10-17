# D1 Migrations

Safe migration discipline for Cloudflare D1 via `wrangler d1 migrations` (SQLite-flavored). Use when changing database schema.

## Follow migration principles

- Treat every schema change as a migration file. Never alter a production database by hand.

- Keep migrations forward-only in production. Fix a bad migration with a new forward migration instead of editing history.

- Keep DDL and DML in separate migrations. Never mix schema changes and data changes in one file.

- Never edit a migration that has already deployed. Create a new migration for the correction.

## Run the safety checklist

- Confirm the migration has a documented rollback plan before applying it.

- Confirm new columns are nullable or carry defaults. Never add a NOT NULL column without a default to a table that already has rows.

- Confirm the data backfill lives in its own migration, separate from the schema change.

- Confirm the migration runs cleanly against a copy of production data, not just an empty local database.

- Confirm no step needs a table rewrite on a large table during peak hours.

## Apply safe change patterns

- Add columns as nullable first, backfill in a separate migration, then add constraints. Run each step as its own migration file.

```sql
-- 0001_add_display_name.sql
ALTER TABLE users ADD COLUMN display_name TEXT

-- 0002_backfill_display_name.sql
UPDATE users SET display_name = username WHERE display_name IS NULL
```

- Rename columns with expand-and-contract in four steps: add the new column, dual-write from the app, backfill old rows, then drop the old column after the app stops reading it.

- Batch backfills by rowid ranges so each statement touches a bounded slice. Commit between batches and make every batch rerunnable.

```sql
-- one batch of a backfill migration
UPDATE users
SET display_name = username
WHERE rowid BETWEEN 1 AND 10000
  AND display_name IS NULL
```

- Verify the backfill completed by counting rows where the new column is still null. Repeat the batch until the count reaches zero.

## Translate Postgres advice for D1

- Drop `CREATE INDEX CONCURRENTLY` from any Postgres-flavored pattern. D1 has no concurrent index creation, so create the index directly and schedule it off-peak for large tables.

```sql
-- D1: plain CREATE INDEX, no CONCURRENTLY
CREATE INDEX IF NOT EXISTS idx_users_email ON users (email)
```

- Respect D1's limited ALTER TABLE. SQLite-flavored D1 supports ADD COLUMN, DROP COLUMN, and RENAME, but not MODIFY or reordering, so plan renames accordingly.

- Rename a column on a small table by recreating the table and copying rows when ALTER TABLE RENAME does not fit the deploy. Keep this pattern for small tables only and test it against a backup first.

```sql
-- recreate-table-and-copy for small tables
CREATE TABLE users_new (id TEXT PRIMARY KEY, email TEXT, display_name TEXT)
INSERT INTO users_new (id, email, display_name)
  SELECT id, email, username FROM users
DROP TABLE users
ALTER TABLE users_new RENAME TO users
```

- Replace `FOR UPDATE SKIP LOCKED` batching with rowid-range batching. D1 has no SKIP LOCKED, so bounded rowid slices are the backfill primitive.

## Verify before applying

- Create migration files with the wrangler CLI. Keep the migrations directory next to wrangler.toml.

```bash
wrangler d1 migrations create nova-db add_display_name
wrangler d1 migrations list nova-db
```

- Test every migration against the local D1 copy before touching remote. Apply with the local flag, run the app against it, and inspect the results.

```bash
wrangler d1 migrations apply nova-db --local
```

- Export a backup of the remote database before applying migrations to it. Keep the backup until the deploy proves healthy.

```bash
wrangler d1 export nova-db --output=backups/nova-db-2026-10-17.sql
wrangler d1 migrations apply nova-db --remote
```

- Run migrations through `muse.exec` and inspect the files with `muse.read`. Follow his build-test discipline: prove it works locally before it touches production.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
