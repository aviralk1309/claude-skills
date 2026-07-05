# Tech Stack — Full Rationale

## Why these defaults

Largest hiring pool, best documentation, lowest bus-factor risk. This skill is used by a wide,
nationwide, mostly-unsupervised audience — defaults need to be safe even without a senior
architect reviewing the choice.

## Frontend

- **React 19 + Next.js 15 (App Router)**: default for anything with SSR/SEO needs, or that will
  grow past a single page. Server Components by default; opt into `"use client"` per-component.
- **Vite + React**: pure SPA, internal tool, no SEO need, no server rendering required.
- **Remix**: only if the user's codebase is already on Remix — don't migrate unasked.
- **Tailwind CSS**: fastest default for consistent styling without a design system already in
  place. CSS Modules for one-off complex components. Don't introduce styled-components/Emotion
  into a Tailwind codebase or vice versa.

## State management

- **TanStack Query** for anything that is server data (fetched, cached, revalidated) — don't put
  server data in Zustand/Redux.
- **Zustand** for client-only UI state (modals, wizards, local toggles).
- **Redux Toolkit**: only continue using it if the team already has Redux; not a default for new
  projects in 2026 given the above two cover most needs with far less boilerplate.

## Backend

- **NestJS**: larger APIs, multiple teams, need for DI/testability/structure enforced by the
  framework.
- **Express**: small services, prototypes, or when the user explicitly wants minimal abstraction.
- **Fastify**: when raw request throughput is the stated priority — has real perf advantages over
  Express at scale but a smaller ecosystem than Nest/Express.

## Databases

- **PostgreSQL**: default relational choice — JSONB support covers most "maybe I need Mongo"
  cases without a second database to operate.
- **MongoDB**: only when data is genuinely document-shaped (deeply nested, schema-variable
  per-record) — not a default just because "NoSQL is easier to start with."
- **Redis**: caching, session store, rate-limiting counters, BullMQ job queues.

## ORM/query layer

- **Prisma**: best DX for Postgres, strong TS types, good migration story.
- **Drizzle**: pick when the user wants SQL-transparency and lighter runtime overhead than Prisma.
- **Mongoose**: standard for MongoDB schema/validation layer.

## Containers/infra

- **Docker + docker-compose**: local dev parity, always.
- **Multi-stage builds**: keep production images small, no build tooling shipped to prod.
- **Kubernetes**: only recommend if the user is already operating at a scale that needs it —
  don't introduce K8s complexity to a project that doesn't need it yet.

## Testing

- **Vitest** (Vite ecosystem) or **Jest** (Next.js/Node default) for unit/integration.
- **Testing Library** for component tests — test behavior, not implementation detail.
- **Playwright** for e2e — cover critical paths only (auth, checkout, core user journey), not
  exhaustive UI coverage.

## Auth

- **Auth.js (NextAuth) / Lucia**: custom auth flows within the JS ecosystem.
- **Clerk/Auth0**: managed auth when the user wants to offload the auth surface entirely.
- Never hand-roll password hashing, session tokens, or JWT validation logic from scratch —
  always use a maintained library for this.

## Observability

- **OpenTelemetry + pino**: structured logs and traces as the default baseline.
- **Sentry**: common add-on for error tracking specifically, layered on top of the above.