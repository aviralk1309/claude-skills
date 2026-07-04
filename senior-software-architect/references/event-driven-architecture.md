# Event-Driven Architecture

## When to reach for it

Use async events when: multiple consumers need the same fact (order placed → billing, shipping, analytics all react independently), you need to decouple producer/consumer deploy cycles, or a workflow spans services and must survive a consumer being temporarily down.

Don't use it for: simple synchronous request/response that has one caller expecting one immediate answer — that's just adding latency and complexity for no gain.

## Core patterns

- **Pub/Sub**: producer emits event, N subscribers react, producer doesn't know/care who's listening. Good for fan-out (Kafka, SNS, Google Pub/Sub).
- **Message queue (point-to-point)**: one producer, one logical consumer group, each message processed once. Good for work distribution/load leveling (SQS, RabbitMQ).
- **Event notification vs event-carried state transfer**: notification says "order 123 changed, go fetch it" (thin event, consumer calls back); state transfer includes the full changed data in the event (fat event, no callback needed but risk of staleness/coupling to payload shape). Pick state transfer when consumers need the data and a callback would create a chatty dependency; pick notification when payload would be large or sensitive.

## Delivery guarantees — pick honestly

| Guarantee | Means | Cost |
|---|---|---|
| At-most-once | May lose messages, never duplicates | Simplest, unacceptable for anything valuable (payments) |
| At-least-once | Never loses, may duplicate | Default for most brokers — **consumer must be idempotent** |
| Exactly-once | Never loses, never duplicates | Very hard/expensive end-to-end; usually "effectively-once" via at-least-once + idempotency, not true exactly-once |

Practical rule: design consumers to be **idempotent** (dedupe by event ID, use upserts not blind inserts) and treat "exactly-once" marketing claims skeptically. This solves 95% of what people actually need exactly-once for.

## Ordering

Most brokers only guarantee order within a partition/shard key, not globally. If order matters (e.g. events for the same order ID must apply in sequence), partition by that entity's ID so all its events land on the same partition/consumer.

## Schema evolution

Event payloads change over time just like APIs (see api-design.md backward-compatibility rule) — treat event schemas with the same discipline:

- **Schema registry** (Confluent Schema Registry, AWS Glue Schema Registry, or equivalent) as the source of truth for event shapes, with compatibility checks enforced at publish time — a producer can't publish a breaking schema change without an explicit version bump.
- **Additive-only for in-flight compatibility**: new optional fields are safe; removing/renaming a field or changing its type breaks any consumer still on the old schema — same rule as REST/GraphQL field changes, just harder to catch because consumers are decoupled and may deploy on a different schedule than the producer.
- **Format choice matters**: Avro/Protobuf enforce schema-on-write and have built-in compatibility modes (backward/forward/full) — safer default for anything beyond a small internal system than raw untyped JSON, where a typo'd field name only surfaces at runtime in a consumer.
- Version the event type itself (e.g. `OrderPlacedV2`) for genuinely breaking changes, and run both versions in parallel until all consumers migrate — same deprecation-window discipline as API versioning.

## Distributed transactions across services

Never use a 2-phase-commit-style distributed transaction across services — it doesn't scale and couples availability. Use the **Saga pattern** instead:
- **Orchestration**: a central saga coordinator calls each service step and issues compensating actions on failure (clearer to trace, one more component to build/own)
- **Choreography**: each service reacts to the previous service's event and emits its own (no central coordinator, but harder to see the whole flow — trace via correlation IDs)

Every step needs a **compensating action** (e.g. "refund payment" to undo "charge payment") since you can't roll back across service boundaries atomically.

## CQRS & Event Sourcing — use sparingly

- **CQRS**: separate models for writes (command) and reads (query). Legit when read shape diverges heavily from write shape (e.g. write is normalized orders, read is a denormalized dashboard). Don't apply CQRS to simple CRUD — it's pure overhead there.
- **Event Sourcing**: store state as an append-only log of events, derive current state by replay, instead of storing current state directly. Gives full audit trail and time-travel debugging, at the cost of much higher complexity (event schema versioning, snapshotting for replay performance, harder ad-hoc queries). Only reach for this when audit/replay is an actual business requirement, not because it sounds rigorous.

## Failure handling

- **Dead-letter queue (DLQ)**: after N retry failures, move the message out of the main queue instead of blocking it or dropping it silently. Alert on DLQ depth.
- **Retry with backoff**: exponential backoff + jitter, capped max retries, not infinite immediate retry (thundering herd against a struggling downstream).
- **Outbox pattern**: to atomically write DB state + publish an event, write both in the same DB transaction to an "outbox" table, then a separate relay publishes from the outbox to the broker. Avoids the classic bug where the DB write commits but the event publish fails (or vice versa).
