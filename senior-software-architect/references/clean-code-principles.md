# SOLID, DRY, Clean Architecture

Apply these when reviewing or writing code-level structure. Name the principle when invoking it — don't just say "best practice."

## SOLID

- **S — Single Responsibility**: a class/module has one reason to change. Test: can you describe its job in one sentence without "and"? If not, split it.
- **O — Open/Closed**: extend behavior via new code (new class implementing an interface), not by editing existing tested code. Strategy pattern, plugins, polymorphism over `switch` on type.
- **L — Liskov Substitution**: a subtype must be usable anywhere its base type is, without breaking caller expectations (no throwing on methods the base type promises, no narrowing preconditions or widening postconditions).
- **I — Interface Segregation**: many small, client-specific interfaces beat one fat interface. If a class implements an interface but leaves half the methods as `throw new NotImplementedException`, the interface is wrong.
- **D — Dependency Inversion**: high-level modules depend on abstractions, not concrete low-level modules. Domain code defines an interface (e.g. `PaymentGateway`); infrastructure implements it. Enables swapping Stripe for Adyen without touching domain code.

Common violations to flag in review:
- 500+ line "Service" or "Manager" classes → SRP violation
- Long `if/else` or `switch` chains on a type field that get a new branch every feature → OCP violation, replace with polymorphism
- Concrete DB/HTTP client instantiated inside business logic (`new PostgresClient()` inside a use case) → DIP violation, inject an interface instead

## DRY — with a caveat

DRY means don't duplicate **knowledge/decisions**, not don't duplicate **text**. Two pieces of code that look similar but represent different business rules that happen to coincide today should stay separate — collapsing them creates false coupling (change one business rule, accidentally change another). Rule of thumb: abstract after the third duplication, not the first (WET → DRY), and only if the duplicated logic represents the same underlying rule.

## Clean / Hexagonal / Onion Architecture

Core idea, shared across all three names: **domain logic at the center, framework/IO at the edges, dependencies point inward.**

```
        ┌─────────────────────────┐
        │   Infrastructure/UI     │  controllers, DB, HTTP clients
        │  ┌───────────────────┐  │
        │  │   Application     │  │  use cases, orchestration
        │  │  ┌─────────────┐  │  │
        │  │  │   Domain    │  │  │  entities, business rules — no framework imports
        │  │  └─────────────┘  │  │
        │  └───────────────────┘  │
        └─────────────────────────┘
```

Practical test of whether this is being followed: can you unit-test the domain layer with zero mocks of HTTP/DB/framework? If domain tests need a running DB or an HTTP mock, the dependency rule is broken.

Ports & Adapters vocabulary (Hexagonal): domain defines "ports" (interfaces) for what it needs (e.g. `OrderRepository`); infrastructure provides "adapters" (`PostgresOrderRepository`). Swappable, testable in isolation.

## When to NOT apply these strictly

Small scripts, prototypes, throwaway internal tools — full layering is overhead with no payoff. Apply SOLID/Clean Architecture rigor to code that will live > 6 months or be touched by > 1 person. Say so explicitly if user's context is a prototype: recommending 4 layers for a weekend hackathon is overengineering.
