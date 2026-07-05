# Backend — Node.js / Express / NestJS

## API design

- REST: nouns for resources (`/orders/:id`, not `/getOrder`), correct status codes (`201` on
  create, `204` on delete-no-body, `409` on conflict, etc.), consistent pagination pattern
  (cursor-based for large collections, offset for small/admin lists).
- Versioning: URL-based (`/api/v1/...`) for public APIs, header-based acceptable for internal.
- Error envelope, consistent across the whole API:
  ```json
  { "error": { "code": "VALIDATION_ERROR", "message": "...", "details": {} } }
  ```
- GraphQL: only when the client genuinely needs flexible nested queries across relations that
  would otherwise require many REST round-trips or heavy over-fetching — not a default choice.

## Validation

- Validate every external input (body, query, params, headers) at the boundary — Zod (or Nest's
  built-in pipes/class-validator) before it reaches business logic.
- Never trust client-supplied IDs for authorization — always re-check ownership/permission
  server-side, even if the client "shouldn't" be able to request it.

## NestJS specifics

- Modules organized by feature/domain, not by technical layer.
- Use DI for services/repositories — makes testing straightforward with mock providers.
- Guards for auth, Interceptors for cross-cutting concerns (logging, transform), Pipes for
  validation — use the framework's structure rather than ad hoc middleware stacking.

## Express specifics

- Centralized error-handling middleware (last in the chain) rather than try/catch + res.send
  repeated in every route.
- `express-validator` or Zod middleware for input validation.
- Keep route files thin — delegate to service/controller functions, don't inline business logic
  in the route handler.

## Auth & sessions

- Hash passwords with `argon2` or `bcrypt` (never roll your own, never store plaintext or
  reversible-encrypted passwords).
- JWT: short-lived access tokens + refresh token rotation, stored httpOnly/secure cookies for
  web clients (not localStorage, which is XSS-exposed).
- Rate-limit login/auth endpoints specifically, separate from general API rate limits.

## Error handling & logging

- Structured logging (pino) with request IDs for tracing a request across services.
- Don't leak internal error details (stack traces, DB errors) to the client response — log them,
  return a generic message + error code.

## Background jobs

- BullMQ (Redis-backed) for anything async: emails, report generation, webhooks retries.
- Idempotency keys for operations that might be retried (payments, webhook handlers).