# Database — PostgreSQL / MongoDB / Redis

## Schema design

- Normalize by default in Postgres; denormalize deliberately only for a measured read
  performance need, and document why.
- Use `JSONB` columns for genuinely variable/nested data within an otherwise relational schema
  instead of reaching for Mongo as a second database.
- Foreign keys enforced at the DB level — don't rely on application code alone for referential
  integrity.

## Migrations

- Always version-controlled (Prisma Migrate, Drizzle Kit, or node-pg-migrate) — never hand-edit
  schema directly against production.
- Migrations should be reversible where feasible; irreversible ones (dropping a column/table)
  get an explicit warning and a backup step before running.

## Indexing

- Index based on actual query patterns (`EXPLAIN ANALYZE` output), not guesses.
- Composite indexes match the query's `WHERE`/`ORDER BY` column order.
- Watch for indexes that help reads but slow every write — don't over-index write-heavy tables.

## Queries

- Parameterized queries always — no string concatenation building SQL, regardless of who's
  asking or why.
- N+1 query prevention: use `include`/`with` (Prisma) or explicit joins, not a query-per-row loop.
- Paginate any query that could return unbounded rows.

## MongoDB specifics

- Schema-per-collection via Mongoose even though Mongo is schemaless — validation and
  discoverability matter more than flexibility for most app data.
- Embed for data that's always read together and rarely updated independently; reference for
  data that grows unbounded or is shared across documents.
- Index fields used in `find`/`sort` — same principle as Postgres, checked via
  `.explain("executionStats")`.

## Redis

- Cache: set explicit TTLs, never cache indefinitely without an invalidation path.
- Sessions: short TTL + sliding expiration on activity.
- Rate limiting: fixed-window or sliding-window counter keyed by user/IP + endpoint.
- Don't use Redis as a system of record for data that needs durability guarantees beyond what
  Redis persistence (AOF/RDB) provides — it's a cache/queue layer, not the primary database.

## Backups & durability

- Automated backups with a tested restore process — an untested backup is not a backup.
- Point-in-time recovery (Postgres WAL archiving) for anything with real durability
  requirements.