# API Design — REST & GraphQL

## Choosing REST vs GraphQL vs gRPC

| | REST | GraphQL | gRPC |
|---|---|---|---|
| Best for | Resource-oriented CRUD, public APIs, caching via HTTP | Complex/nested data needs, many client shapes (web+mobile), avoiding over/under-fetching | Internal service-to-service, low-latency/high-throughput, strongly-typed contracts |
| Cost | Over/under-fetching, N+1 round trips for nested data | Query complexity/cost control needed, harder HTTP caching, N+1 resolver problem | Poor browser support without a proxy (grpc-web), less human-readable than JSON, steeper tooling setup |
| Don't pick if | Client needs deeply nested, variable-shape data | Team has no bandwidth for schema governance, or need simple public CRUD API | Public-facing API with polyglot/unknown consumers, or team unfamiliar with protobuf tooling |

Default to REST unless there's a concrete reason for GraphQL (multiple client shapes, deep nesting) or gRPC (internal, latency-sensitive, you control both ends — see microservices.md for when gRPC beats REST internally). Don't add GraphQL or gRPC because it's trendy.

## REST — standards to actually enforce

- **Resources are nouns, plural**: `/orders`, `/orders/{id}`, not `/getOrder` or `/order`.
- **HTTP methods carry meaning**: GET (safe, idempotent), PUT (idempotent replace), PATCH (partial update), POST (create / non-idempotent action), DELETE (idempotent remove). Don't use GET for mutations.
- **Status codes mean something**: 200 (ok), 201 (created, + `Location` header), 204 (no content), 400 (bad request/validation), 401 (unauthenticated), 403 (unauthorized), 404 (not found), 409 (conflict), 422 (semantically invalid), 429 (rate limited), 500 (server error). Don't return 200 with an error in the body.
- **Versioning**: prefer a URL prefix (`/v1/orders`) or a header (`Accept: application/vnd.api.v1+json`) over no versioning. Breaking changes require a new version, not a silent field change.
- **Pagination**: cursor-based for large/growing datasets (stable under inserts), offset-based only for small/static lists. Always cap page size server-side.
- **Filtering/sorting**: query params (`?status=paid&sort=-created_at`), not custom endpoints per filter combination.
- **Idempotency for unsafe operations**: POST that creates a payment/order should accept an `Idempotency-Key` header so retries don't double-create.
- **Errors**: consistent shape across the API, e.g. `{ "error": { "code": "...", "message": "...", "details": [...] } }`. Don't leak stack traces to clients.
- **HATEOAS**: rarely worth full implementation in practice — mention it exists, don't force it unless the user is building a public API meant for long-term evolvability.

## GraphQL — standards to actually enforce

- **Schema-first design**: types model the domain, not the DB schema 1:1.
- **N+1 problem**: always pair nested resolvers with a batching loader (DataLoader pattern) — resolving `orders { customer { name } }` for 100 orders must not fire 100 separate customer queries.
- **Query cost limiting**: cap query depth and complexity server-side, or a client can request an exponential-cost query and DoS you. Non-negotiable for public-facing GraphQL.
- **Pagination**: use Relay-style cursor connections (`edges`, `node`, `pageInfo`) for consistency, unless there's a reason not to.
- **Errors**: partial responses are valid GraphQL (`data` + `errors` both present) — don't treat every resolver error as a full request failure.
- **Avoid a single mega-schema with no ownership** in a multi-team org — federation (Apollo Federation / schema stitching) if multiple teams own different parts of the graph.
- **Mutations should be named as actions** (`createOrder`, not `order` with a write side effect), and return the affected object so clients can update caches without a refetch.

## Cross-cutting, both styles

- Auth at the gateway/edge, not duplicated per-endpoint logic (see auth.md)
- Rate limiting per client/API key, not just globally
- Contract-first when multiple teams consume the API: OpenAPI spec (REST) or SDL schema (GraphQL) as the source of truth, generate clients/docs from it — not the reverse
- Backward compatibility: additive changes (new optional field) are safe; removing/renaming a field or changing its type is breaking — needs a version bump or deprecation window
