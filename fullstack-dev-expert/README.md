# fullstack-dev-expert

A Claude Skill that turns Claude into a senior full-stack engineer for both the **JS/TS**
ecosystem (React, Next.js, Node.js, Express, NestJS) and the **Python** ecosystem
(Django, DRF, Celery) — plus the shared data/infra layer (PostgreSQL, MongoDB, Redis, Docker).

It explains code, builds features, refactors, debugs, and tunes performance, while pushing
toward current, production-grade industry defaults instead of tutorial-level shortcuts —
and adjusts its recommendations based on whether you're building for a **product** you own
long-term or **delivering to a client** who takes over maintenance.

## Why this exists

Most "act as a senior engineer" prompts are one-off and inconsistent. This is a structured
Claude Skill: a `SKILL.md` entry point plus focused reference files, so Claude loads exactly
the depth it needs for the task instead of guessing from a single giant prompt.

## What's inside

```
fullstack-dev-expert/
├── SKILL.md                        # Entry point: routing, defaults, standards
└── references/
    ├── tech-stack.md                # Full stack rationale + alternatives
    ├── frontend.md                  # React/Next.js/CSS specifics
    ├── backend.md                   # Node/Express/NestJS specifics
    ├── python-django.md             # Django/DRF specifics
    ├── database.md                  # Postgres/Mongo/Redis specifics
    ├── devops.md                    # Docker/CI-CD/observability
    ├── security-checklist.md        # Non-negotiable security defaults
    └── org-context.md               # Product-based vs. service-based decision differences
```

`evals/evals.json` (not packaged into the `.skill` file) holds test prompts used to check the
skill triggers correctly and produces the expected quality of answer — useful if you want to
verify changes before opening a PR.

## Install

1. Download `fullstack-dev-expert.skill` from [Releases](../../releases) or build it yourself
   (see below).
2. In Claude (claude.ai, Claude Code, or Claude Desktop), add it as a custom skill via
   Settings → Capabilities → Skills, or drop the extracted folder into your skills directory
   if you're self-managing skills.

## Building the `.skill` file yourself

```bash
git clone https://github.com/<your-username>/fullstack-dev-expert.git
cd fullstack-dev-expert
# uses Anthropic's skill-creator packaging tooling
python3 -m scripts.package_skill fullstack-dev-expert
```

## Example prompts this should handle well

- "Why is this React component re-rendering even though it's wrapped in `React.memo`?"
- "Add a PATCH endpoint to update a user's profile in my NestJS app."
- "My Postgres query is slow, here's the query and table size."
- "Refactor this Express route, it's mixing validation, business logic, and DB calls."
- "Should I use MongoDB or Postgres for an e-commerce app?"
- "In my Django DRF app, users can view other users' orders by changing the ID in the URL."
- "We're an agency building this for a client with a 2-person in-house team — keep it simple
  or go microservices?"

## Contributing

Contributions welcome — especially:
- Additional stack references (Java/Spring, PHP/Laravel, Go, Ruby on Rails, mobile)
- More eval cases (see `evals/evals.json` for the format)
- Corrections to anything that's gone stale as frameworks evolve

Please open an issue describing what you'd like to add/change before submitting a large PR,
so we can agree on scope first. See `CONTRIBUTING.md` for details.

## License

MIT — see `LICENSE`.