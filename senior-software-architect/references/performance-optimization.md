# Performance Optimization

## Rule zero: measure before optimizing

Never guess where the bottleneck is. Profile first (APM tool, DB `EXPLAIN ANALYZE`, flame graphs, load testing). Optimizing the wrong thing wastes effort and adds complexity for no gain. Get a baseline number before and after every change.

## Where bottlenecks usually actually are (in rough frequency order)

1. **N+1 queries** — one query per row in a loop instead of one batched query. The single most common real-world perf bug. Fix: eager loading / batching / DataLoader.
2. **Missing or wrong DB index** — see database-design.md.
3. **No caching on expensive/repeated reads.**
4. **Synchronous work that could be async** — e.g. sending an email inline in a request handler instead of queuing it.
5. **Chatty network calls** — many small round trips instead of one batched call, especially across a service boundary.
6. **Unbounded payloads** — returning entire datasets instead of paginating.

## Caching

- **Cache-aside (lazy load)**: app checks cache, on miss reads DB and populates cache. Simplest, most common. Risk: cache stampede on a popular key expiring — mitigate with jitter on TTL or a lock/single-flight on miss.
- **Write-through**: write to cache and DB together. Cache always warm, extra write latency.
- **Write-behind**: write to cache, async flush to DB. Fast writes, risk of data loss if cache dies before flush.
- **Invalidation is the hard part, not the caching itself.** Strategies: TTL-based (simple, accepts staleness window), event-based invalidation (precise, more moving parts), version/key-based (append a version to the cache key, bump version on write — avoids explicit delete races).
- **Where to cache**: CDN (static assets, cacheable API responses) → application-level cache (Redis) → DB query cache → in-process cache (careful: doesn't work across multiple instances without a shared layer or explicit invalidation broadcast).

## Scaling levers, cost-ordered (same list as architecture.md, perf lens)

1. Fix the actual slow query/algorithm (usually the real fix — check Big-O of hot-path code, not just infra)
2. Add appropriate index
3. Add caching
4. Vertical scale
5. Horizontal scale (requires stateless services)
6. Read replicas
7. Sharding (last resort)

## Async & concurrency

- Move non-critical-path work (emails, notifications, analytics events, report generation) off the request/response path into a queue/background worker. The user shouldn't wait on work they don't need the result of immediately.
- Use connection/thread pools sized to actual downstream capacity, not arbitrarily large — an oversized pool just moves the bottleneck downstream and can take down the dependency.
- Batch operations where the underlying system supports it (bulk insert vs N single inserts; batched API calls vs N single calls).

## Frontend/API-facing perf (if in scope)

- Pagination and field selection (don't return more than the client needs) — especially relevant for GraphQL over-fetching and REST endpoints returning full objects when a summary would do.
- Compression (gzip/br) on API responses.
- HTTP/2 or HTTP/3 for reduced connection overhead on many small requests.
- CDN + proper cache headers (`Cache-Control`, `ETag`) for anything cacheable.

## Load testing

Before claiming a system "scales," load test it (k6, Locust, Gatling) against realistic traffic shapes (not just steady load — include bursts). Know your actual capacity numbers (max sustained QPS, p95/p99 latency under load) rather than assuming headroom exists.
