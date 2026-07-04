# Testing Strategy

## Test pyramid — proportions, not just types

| Layer | Scope | Speed | Volume |
|---|---|---|---|
| Unit | Single function/class, no I/O, no DB, no network | ms | Most tests — 70%+ |
| Integration | Component + real dependency (DB, cache, queue) — via testcontainers, not mocks | seconds | Fewer — 20% |
| Contract | Verify API/event schema between two services matches, without running both | seconds | Per service boundary |
| E2E | Full stack, real (or near-real) environment, user-facing flow | slow, minutes | Fewest — <10%, critical paths only |

Inverted pyramid (mostly E2E, few unit tests) is the #1 real-world testing anti-pattern: slow CI, flaky, hard to localize failure. If review sees this, flag it first — bigger blast radius than any individual test's quality.

## What belongs at each layer

- **Unit**: pure domain logic, business rules, edge cases of a single function. If a domain layer (see architecture-and-decomposition.md) needs a DB or HTTP mock to unit-test, the dependency rule is already broken — fix the architecture, not the test.
- **Integration**: repository implementations against a real (containerized) DB, cache invalidation logic against real Redis, message serialization against a real broker. Use testcontainers / ephemeral Docker instances, not a shared persistent test DB — shared state across test runs causes flaky, order-dependent failures.
- **Contract**: for service-to-service or event producer/consumer pairs, verify the contract independently (Pact, or schema-registry compatibility checks) instead of standing up both services to test them together. Catches breaking changes at PR time, not at deploy time.
- **E2E**: only the handful of flows that matter most to the business (checkout, signup, payment) — each E2E test is expensive to write and maintain, and slow/flaky in proportion to how much real infra it touches.

## Mocking philosophy

- Mock at architectural boundaries (ports/interfaces — see Hexagonal Architecture in clean-code-principles.md), never mock internals of the code under test.
- Don't mock what you don't own if avoidable for integration-level tests — a mocked DB driver tells you nothing about whether the real query works. Use a real (containerized) instance instead.
- Over-mocked unit tests that mock 5 collaborators to test 1 line of logic are a smell — usually means the unit under test has too many dependencies (SRP violation).

## Flaky tests

- Zero tolerance policy in principle, honest triage in practice: quarantine (skip + ticket, not delete) a flaky test immediately rather than let it erode trust in the whole suite — a team that ignores red CI because "it's probably just flaky" has already lost the safety net.
- Common causes: shared mutable state between tests, unbounded waits/sleeps instead of polling for a condition, real network calls in what should be an integration/unit test, test execution order dependency.
- Fix at the root cause, don't just add retries as a permanent fix — retries mask a real bug (race condition, non-idempotent setup) that will eventually bite in production too.

## Coverage — a signal, not a target

- Coverage percentage tells you what ran, not what was verified correctly. 100% coverage with no assertions on outcomes is worthless.
- Useful as a floor to catch obviously untested code paths (new code with 0% coverage on a PR), not as a KPI to optimize past ~80%. Chasing 100% usually means testing trivial getters/setters instead of business logic.
- Mutation testing (Stryker, PIT) is a stronger signal than line coverage if the team has budget for it — catches "covered but not actually verified" cases.

## TDD — when it earns its cost

- Genuinely useful for: complex business logic with many edge cases (pricing rules, tax calculation, state machines) where writing the test first forces clarifying the spec.
- Low payoff for: CRUD boilerplate, UI layout, exploratory/prototype code where the design itself is still changing test-first here just means rewriting tests repeatedly.
- Don't mandate TDD org-wide as dogma — recommend it for the code where its actual benefit (design pressure + regression safety net) outweighs the overhead.

## CI gating

- PR merge should require: unit + integration tests green, contract tests green for touched boundaries, lint/type-check green. E2E suite can gate release/deploy rather than every PR if it's slow — don't block fast iteration on a 20-minute E2E run for every commit.
- Test the migration, not just the code: a schema migration (see database-design.md) should have its own test verifying it applies cleanly against a snapshot of production-shaped data, not just "the app still boots."
- Track flaky-test rate and CI runtime as first-class metrics — a CI suite that takes 45 minutes or fails 1-in-10 runs for no code reason is a team velocity tax, treat it like a production incident.

## Anti-patterns to flag

- Ice-cream-cone pyramid (mostly E2E/manual, few unit tests)
- Tests that assert on implementation details (mock call counts, internal state) instead of observable behavior — brittle, breaks on refactor even when behavior is unchanged
- Shared/global test fixtures mutated across tests without reset — causes order-dependent flakiness
- Snapshot tests approved blindly on diff without reviewing what changed — becomes a rubber stamp, not a real check
- No test for the failure path (only happy path tested) — error handling, timeouts, retries are exactly where production incidents originate
