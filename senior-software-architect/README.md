# senior-software-architect

Claude Skill. Makes Claude act like staff/principal engineer for system design, architecture review, and infra decisions.

## Covers
- Monolith vs microservices decomposition
- SOLID / DRY / Clean Architecture
- REST & GraphQL API design
- Event-driven architecture (sagas, CQRS, event sourcing, delivery guarantees)
- Microservices (data ownership, resilience patterns, observability)
- Database design (schema, indexing, scaling, migrations)
- AuthN/AuthZ (OAuth2/OIDC, RBAC/ABAC, session vs token)
- Performance optimization (caching, N+1, scaling levers)
- Testing strategy (test pyramid, contract tests, flaky tests, TDD, CI gating)
- Security/AppSec (threat modeling, OWASP classes, secrets, supply chain, data protection)
- CI/CD & IaC (deploy strategies, rollback, GitOps, environment/config management)

## Install
Drop `senior-software-architect/` folder into your Claude skills directory (`/mnt/skills/user/` on Claude.ai, or wherever Claude Code loads skills from), or upload the packaged `.skill` file if using Claude.ai skill upload.

## Structure
```
senior-software-architect/
├── SKILL.md                              # persona, rules, routing table
├── CONTRIBUTING.md                       # contribution guide, style rules
├── evals/
│   ├── evals.json                        # trigger-accuracy + content eval cases
│   └── README.md                         # how to run evals
└── references/
    ├── architecture-and-decomposition.md
    ├── clean-code-principles.md
    ├── api-design.md
    ├── event-driven-architecture.md
    ├── microservices.md
    ├── database-design.md
    ├── auth.md
    ├── performance-optimization.md
    ├── testing-strategy.md
    ├── security-appsec.md
    └── cicd-and-iac.md
```

SKILL.md stays small and routes to the relevant reference file per question — keeps context usage low, avoids dumping all 11 domains into every response.

## Contributing

See `CONTRIBUTING.md` for style rules and how to add or fix a reference file. `evals/` has trigger-accuracy test cases — run them (or at least eyeball the positive/negative cases manually) before merging changes to `SKILL.md`'s description or routing table.

## License
MIT
