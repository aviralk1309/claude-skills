# DevOps — Docker / CI-CD / Observability

## Docker

- Multi-stage builds: build stage with full toolchain, final stage copies only the built
  artifact + runtime deps — keeps prod images small and reduces attack surface.
- Non-root user in the final image (`USER node` or equivalent) — never run production
  containers as root.
- `.dockerignore` mirrors `.gitignore` plus `node_modules`, `.env`, build caches.
- Pin base image versions (`node:20.11-slim`, not `node:latest`) for reproducible builds.

## docker-compose (local dev)

- One service per container (app, Postgres, Redis, Mongo as needed) with named volumes for
  data persistence across restarts.
- `.env` file for local secrets, never committed; `.env.example` committed with placeholder
  values so new contributors know what's required.

## CI/CD (GitHub Actions default)

- Pipeline stages: install → lint → typecheck → test → build → (deploy on merge to main).
- Fail fast: lint/typecheck before the slower test/build stages.
- Cache dependencies (`actions/cache` keyed on lockfile hash) to keep CI runs fast.
- Run migrations as an explicit, separate CD step — never as a side effect of app boot in
  production.

## Observability

- Structured JSON logs (pino) with a request ID propagated through the call chain — makes it
  possible to trace one request across services/logs.
- OpenTelemetry traces for cross-service latency visibility once there's more than one service.
- Sentry (or equivalent) for error aggregation and alerting — errors should be visible without
  needing to grep logs.
- Health check endpoint (`/healthz`) that actually checks DB/Redis connectivity, not just
  "process is running."

## Deployment patterns

- Blue-green or rolling deploys to avoid downtime on release.
- Feature flags for risky changes, so a bad feature can be disabled without a full rollback.
- Rollback plan defined before deploying, not improvised after something breaks.