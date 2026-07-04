# Database Design

## SQL vs NoSQL — decide on access pattern, not hype

| Use SQL (Postgres/MySQL) when | Use NoSQL when |
|---|---|
| Data is relational, needs joins/transactions | Access pattern is key-value or single-item lookups at massive scale |
| Strong consistency matters (money, inventory counts) | Schema is genuinely variable/document-shaped and doesn't need cross-entity queries |
| You're not sure yet | You have a specific, known scale/latency need SQL can't meet |

Default to SQL unless there's a concrete reason not to. Postgres in particular handles JSONB, full-text search, and decent scale — most systems that reach for NoSQL "for scale" never hit the scale that justified it.

## Schema design

- **Normalize by default** (3NF): each fact stored once, avoid update anomalies. Denormalize deliberately, later, for specific read-heavy hot paths — with a plan for keeping the denormalized copy in sync (trigger, event, batch job), not as the starting design.
- **Every table needs a primary key**; prefer a surrogate key (UUID/auto-increment) over natural keys that might change (email, SSN).
- **Foreign keys enforced at the DB level**, not just in application code — the DB is the last line of defense against orphaned/inconsistent data.
- **Model the actual invariants**: if "an order must have at least one line item," that's better enforced by schema/constraint than hoped for in app code.

## Indexing

- Index columns used in `WHERE`, `JOIN`, and `ORDER BY` clauses — but every index costs write performance and storage, so don't index everything speculatively.
- **Composite indexes**: column order matters — put the highest-selectivity / most-frequently-filtered column first, matching actual query patterns.
- Use `EXPLAIN ANALYZE` (Postgres/MySQL) before assuming an index is needed or working — measure, don't guess.
- Watch for: missing index causing full table scans on a hot query; too many indexes slowing writes; index on a column with very low cardinality (e.g. boolean) — usually not selective enough to help.

## Scaling reads/writes

1. **Connection pooling** first (PgBouncer, etc.) — cheap, huge win, do this before anything else.
2. **Read replicas** for read-heavy workloads — route reads there, writes to primary. Accept replication lag (eventual consistency for reads) or route consistency-critical reads to primary.
3. **Caching layer** (Redis/Memcached) in front of hot reads — see performance-optimization.md for invalidation strategy.
4. **Partitioning/sharding** — last resort. Options:
   - Vertical partitioning: split tables by feature area onto different DBs
   - Horizontal sharding: split rows across DBs by a shard key (customer ID, region) — big complexity jump: cross-shard queries/transactions become hard, resharding later is painful. Pick the shard key carefully upfront; getting it wrong is expensive to fix.

## Multi-region / geo-distributed data

Only reach for this when there's a concrete requirement: data residency/compliance (data must stay in-region), latency for a genuinely global user base, or disaster recovery across regions — not by default "for scale."

| Approach | How | Cost |
|---|---|---|
| Single region + read replicas elsewhere | Writes to one region, reads served locally where possible | Simplest; writes still pay cross-region latency, doesn't satisfy strict data-residency needs |
| Active-passive multi-region | Full standby region, promoted on failover | DR-focused; standby capacity sits mostly idle, failover has some cutover time |
| Active-active multi-region | Writes accepted in multiple regions | Needs conflict resolution (last-write-wins, CRDTs, or app-level merge) or a globally-consistent DB (Spanner, CockroachDB, DynamoDB global tables) — big jump in complexity and cost |

CAP theorem is unavoidable here: a network partition between regions forces a choice between availability (accept writes in both, resolve conflicts later) and consistency (reject writes until partition heals). Pick deliberately based on the actual business tolerance for stale/conflicting data — don't assume a globally-consistent DB product removes this tradeoff, it just moves the cost into that product's latency/throughput profile.

## Transactions & consistency

- Wrap multi-step writes that must succeed/fail together in a DB transaction — don't rely on app-level "hope nothing fails between these two writes."
- Understand isolation levels: default (Read Committed in Postgres) is fine for most cases; use `SERIALIZABLE` or explicit locking only when you've identified a real race condition (e.g. double-booking, overselling inventory).
- Across services, there is no cross-service DB transaction — use the Saga pattern (see event-driven-architecture.md).

## Migrations

- Every schema change is a migration file, checked into version control, applied via a migration tool (Flyway, Alembic, Prisma Migrate, etc.) — never hand-edited on the production DB.
- Backward-compatible migrations for zero-downtime deploys: add-nullable-column → backfill → make non-null, rather than one breaking step. Don't drop/rename a column the currently-running app version still reads.
