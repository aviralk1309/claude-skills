# Product-Based vs. Service-Based Context

Same technical skill, different constraints. Check which situation applies (ask if unclear)
before recommending architecture, tooling, or process — the "right" answer can differ.

## Product-based companies (own the codebase long-term)

**Optimize for:** long-term velocity, internal team ownership, compounding quality.

- Invest in stronger typing, higher test coverage, internal tooling/scripts — the payoff
  compounds over years of the same team owning this code.
- Tech debt gets tracked (even informally) and paid down deliberately, not ignored until it's a
  crisis.
- Gradual migrations preferred over rewrites — a working system evolving beats a risky big-bang
  replacement, in most cases.
- Documentation can be leaner internally (the team that wrote it is the team maintaining it) —
  but architecture decisions (ADRs) are still worth recording for future team members.
- Freer to pick less mainstream tools if the team has genuine expertise and it's a real
  productivity win — you're not handing this to a stranger.

## Service/consulting-based companies (build → deliver → client owns it)

**Optimize for:** the client team's ability to maintain this without you.

- Favor mainstream, widely-taught framework/library choices over clever or niche ones — the
  client's future hires need to be able to find help for this stack.
- Handoff documentation is a deliverable, not an afterthought: README with setup/run/deploy
  steps, architecture overview, environment variable reference, "how to do common task X."
- Avoid infra the client can't operate (e.g. don't introduce Kubernetes for a client whose team
  has never run it, unless that's explicitly what they asked for and can support).
- Respect contract scope — don't silently add scope ("gold-plating") beyond what was agreed,
  even if it seems like an improvement; flag the idea instead and let the client decide, since
  it likely affects timeline/budget.
- Code review/style should be boring and consistent — a client engineer picking this up cold
  should be able to follow it without decoding personal cleverness.
- Build in a clean handoff point: final walkthrough, access transfer checklist (repo, hosting,
  domains, API keys, monitoring dashboards), and a defined support/warranty window if
  applicable.

## Signals to look for in the conversation

- "our product," "our users," "long-term roadmap," "we own this" → product-based framing.
- "client," "deliverable," "handoff," "SOW," "the project," "their team will maintain this" →
  service-based framing.
- If genuinely ambiguous and the choice matters (e.g. recommending Kubernetes vs. a managed
  PaaS, or a niche framework vs. a mainstream one), ask in one line rather than guessing.

## How this changes concrete recommendations (examples)

| Decision | Product-based lean | Service-based lean |
|---|---|---|
| Framework choice | Whatever the team is most productive in | Whatever the client's team can hire for/maintain |
| Test coverage | Invest heavily, pays off over years | Cover critical paths; match what was scoped |
| Infra complexity | Can justify K8s/microservices if team can run it | Default to simpler managed infra unless client asked otherwise |
| Documentation | Lean internal docs + ADRs | Full handoff docs as a deliverable |
| Refactoring scope | Can refactor broadly for long-term health | Stay within contracted scope; propose extras separately |