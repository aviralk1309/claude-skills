# Microservices — Implementation Specifics

(Read `architecture-and-decomposition.md` first for whether to use microservices at all.)

## Data ownership — the non-negotiable rule

Each service owns its own datastore; no other service touches it directly, ever — not even read-only. Cross-service data access goes through the owning service's API or via events it publishes. Shared databases across services is the #1 cause of "distributed monolith" (can't deploy independently because schema changes ripple).

Consequence: joins across service boundaries don't exist as DB joins. Either:
- Query the owning service's API and join in application code (fine for low volume/latency-tolerant cases)
- Maintain a denormalized local read-copy, kept in sync via events from the owning service (better for high volume/low latency reads)

## Service-to-service communication

- **Sync (REST/gRPC)** when caller needs an immediate answer to proceed. Keep call chains shallow — depth > 2-3 synchronous hops means one slow service cascades latency/failures up the whole chain.
- **Async (events/queue)** when caller doesn't need to wait, or multiple services need to react to a fact. Default to this for anything that doesn't strictly need a synchronous answer.
- **gRPC over REST** internally when you control both ends and want lower latency/smaller payloads (protobuf) — REST/JSON still wins for public-facing or polyglot-uncertain consumers.

## Resilience patterns (mandatory at scale, not optional polish)

- **Circuit breaker**: stop calling a downstream that's failing repeatedly, fail fast instead of piling up timeouts. Half-open state to test recovery.
- **Timeouts on every network call**: no call without a timeout, ever. An unbounded call is a resource leak waiting to cascade.
- **Bulkhead isolation**: separate connection pools/thread pools per downstream dependency, so one slow dependency can't exhaust resources needed to call a different, healthy one.
- **Retries with backoff + jitter**, bounded, and only on idempotent operations (see event-driven-architecture.md on idempotency).
- **Graceful degradation**: define a fallback (cached/stale data, reduced feature) rather than a hard failure when a non-critical dependency is down.

## Service discovery & config

- Discovery: DNS-based (simple, works for most), or a service mesh (Istio/Linkerd) once you have dozens of services and need consistent mTLS/observability/retries injected uniformly rather than per-service.
- Config: externalized (env vars, config service — 12-factor), never baked into the image per environment.

## Observability — required, not optional, in microservices

Without these three, debugging a multi-service system is guesswork:
- **Distributed tracing** (OpenTelemetry + Jaeger/Tempo/etc.) — propagate a correlation/trace ID through every hop, sync and async.
- **Centralized structured logging** — logs from all services in one searchable place, correlated by trace ID.
- **Metrics/dashboards** per service — latency percentiles (p50/p95/p99, not just average), error rate, saturation (the "RED" method: Rate, Errors, Duration).

## Deployment

- Each service independently deployable and independently rollback-able. If deploying service A requires deploying service B at the same time, they're not actually decoupled.
- Backward/forward compatible API contracts across a deploy (don't require the caller and callee to update in lockstep) — additive changes only for in-flight compatibility; use consumer-driven contract tests (e.g. Pact) to catch breaking changes before they ship.

## Team topology

Conway's Law is real: your service boundaries will drift to match your team boundaries whether you plan it or not. Design service ownership to map to team ownership deliberately (one team, one or a few services) rather than fighting the drift.
