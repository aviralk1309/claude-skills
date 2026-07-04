# Architecture & Decomposition

## Default advice: modular monolith first

Most systems (< 20 engineers, < ~50k req/s) should start as a **modular monolith**: one deployable, internally split into modules with clear boundaries (by domain, not layer). Enforce boundaries with internal package/module rules (no cross-module DB access, only public interfaces). This gets 80% of microservices' organizational benefit at 10% of the operational cost.

Split into services only when you hit one of:
- **Team scaling wall**: multiple teams blocked on each other's deploys/code in the same repo
- **Differential scaling need**: one component needs 100x the resources of the rest
- **Differential release cadence**: one component must ship 10x/day, another ships monthly
- **Compliance/isolation boundary**: PCI scope, data residency, must run in different trust zone

If none of these are true yet, decomposing is premature — the cost (network calls, distributed transactions, deployment complexity, observability tax) exceeds the benefit.

## How to find service boundaries (when it's time)

Use **Domain-Driven Design** bounded contexts, not technical layers:
- Model around business capabilities ("Orders", "Inventory", "Billing"), never around technical tiers ("API layer", "DB layer")
- Draw a **Context Map**: which contexts talk to which, and via what (sync API, async event, shared kernel — avoid shared kernel/shared DB across boundaries)
- Rule of thumb: a service should be ownable end-to-end by one team, deployable independently, and have its own datastore

Warning sign you got boundaries wrong: two services must deploy together to avoid breaking, or one query needs to join across two services' data synchronously. That's a distributed monolith — either merge them or fix the boundary.

## Layering within a service (Clean/Hexagonal/Onion)

Standard structure regardless of framework:
```
domain/        - entities, value objects, business rules. Zero framework deps.
application/   - use cases / application services, orchestrate domain objects
infrastructure/- DB repos, external API clients, framework glue
interface/     - HTTP controllers, GraphQL resolvers, CLI, message consumers
```
Dependency rule: outer layers depend inward, never the reverse. Domain layer imports nothing from infrastructure or interface. This is what makes domain logic testable without a DB or HTTP server.

## Choosing an architecture style — decision table

| Style | Use when | Cost |
|---|---|---|
| Modular monolith | Default; small-mid team, single deploy cadence acceptable | Less parallel scaling of teams |
| Microservices | Hit team/scaling/cadence wall above | Distributed systems tax: network, observability, data consistency |
| Event-driven | Need loose coupling, async workflows, multiple consumers of same fact | Harder to trace/debug, eventual consistency |
| Serverless/FaaS | Spiky/unpredictable load, want zero idle-cost | Cold starts, vendor lock-in, harder local dev |
| CQRS+Event Sourcing | Complex domain with audit/replay needs, divergent read/write shapes | Much higher complexity, steep learning curve — don't default to this |

## Scalability levers, in order of cost/complexity

1. Vertical scale (bigger instance) — cheapest, do this first, has a ceiling
2. Horizontal scale + load balancing (stateless services required)
3. Caching (see performance-optimization.md)
4. Read replicas / read-write split
5. Sharding / partitioning — last resort, big complexity jump, hard to undo

Don't skip to step 5 because it sounds impressive. Most systems never need it.
