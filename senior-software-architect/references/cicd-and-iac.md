# CI/CD & Infrastructure-as-Code

## Pipeline stages — standard shape

```
commit → build → unit+integration tests → SAST/SCA/secrets scan (security-appsec.md)
        → package/image → deploy to staging → contract/E2E tests (testing-strategy.md)
        → deploy to prod (progressive) → post-deploy verification
```

Fail fast: cheapest/fastest checks first (lint, unit tests) before expensive ones (E2E, full integration). Don't spend 10 minutes on E2E before a 10-second lint failure would've caught it.

## Deploy vs release — decouple them

- **Deploy**: new code is running in production infrastructure.
- **Release**: new code is actually serving user traffic / the feature is visible.

Decoupling these lets you deploy continuously (low risk, code is dark/inactive) and release on a separate decision (business timing, gradual rollout) via feature flags — rather than every deploy being a release event. This is what makes multiple-deploys-per-day sustainable without multiple-releases-per-day risk.

## Deployment strategies

| Strategy | How | Rollback speed | Cost |
|---|---|---|---|
| Rolling | Replace instances gradually, old+new versions coexist briefly | Medium — must roll forward or back through instances | Low infra cost, needs backward-compatible API/DB during transition |
| Blue-green | Full second environment, switch traffic all at once | Fast — flip router back | 2x infra cost during cutover |
| Canary | Small % of traffic to new version, expand gradually if healthy | Fast — pull canary traffic | Needs traffic-splitting + good metrics to judge "healthy" |
| Feature flags | Code deployed dark, enabled via config per user/%/segment | Instant — flip flag off | Flag debt if not cleaned up after full rollout |

Canary + feature flags together is the strongest combo for high-risk changes: canary catches infra/perf regressions, flags catch business-logic regressions, and both roll back without a redeploy.

Any strategy that runs old and new versions simultaneously (rolling, blue-green during cutover, canary) requires the API and DB schema to be backward-compatible for that window — see the migration guidance in database-design.md (additive-first schema changes) and microservices.md (backward/forward compatible contracts). This is the most common real-world cause of "deploy broke prod" — not the new code itself, but old code hitting a schema/contract it wasn't written for.

## Rollback

- Rollback path must be as tested as the forward path — "we'll just redeploy the old version" often fails in practice because a DB migration already ran and isn't backward-compatible with the old code.
- Automate rollback trigger on health-check/error-rate threshold breach post-deploy, don't rely on someone noticing a dashboard.
- Keep the last N known-good artifacts/images readily deployable — rebuilding from source under incident pressure is slower and riskier than redeploying a pinned artifact.

## Infrastructure-as-Code

- **Everything in code, checked into version control** — no manual console/ClickOps changes to any environment that matters. Manual drift is the #1 cause of "works in staging, breaks in prod" for infra reasons.
- **Declarative over imperative**: Terraform/Pulumi/CloudFormation describe desired end state; the tool figures out the diff and apply plan. Preferred over imperative scripts (bash provisioning) for anything beyond a single throwaway box — declarative is idempotent and diffable by default.
- **Plan before apply, always reviewed**: `terraform plan` (or equivalent) output reviewed in PR before `apply` — infra changes get the same review rigor as code changes, since a bad apply can take down prod as fast as bad code.
- **State management**: remote state (S3+DynamoDB lock, Terraform Cloud, etc.), never local state files — local state causes drift/conflicts the moment more than one person touches infra, and is a single point of loss if the laptop dies.
- **Modules, not copy-paste**: reusable modules for repeated patterns (a "service" module used by every microservice's infra) — same DRY principle as code (clean-code-principles.md), same caveat: abstract the third time it repeats, not the first.
- **Environment parity**: staging should mirror prod's topology (even if smaller scale) — different topology (e.g. no message queue in staging, one exists in prod) means staging tests don't actually validate prod behavior.

## GitOps (for Kubernetes-heavy setups)

- Git is the single source of truth for desired cluster state; a controller (ArgoCD, Flux) continuously reconciles cluster to match Git, rather than `kubectl apply` run ad hoc from laptops/CI.
- Every change is a Git commit — full audit trail, easy rollback (`git revert`), no separate "who changed what in the cluster" investigation needed.
- Push-based CI triggering `kubectl apply` still works for smaller setups; GitOps pull-based reconciliation earns its complexity once there are multiple clusters/environments needing consistent drift detection.

## Environment/config management

- 12-factor: config in environment variables or a config service, never hardcoded per environment in the image/artifact — the same build artifact should be promotable from staging to prod unchanged, only config differs.
- Environment-specific secrets pulled from a secrets manager at deploy/runtime (security-appsec.md), never baked into the artifact.
- Feature-flag service (LaunchDarkly, Unleash, or a simple in-house table) for anything needing gradual rollout, A/B testing, or kill-switch capability — don't hardcode `if (user.id == 123)` staging-only logic into the codebase.

## Observability tie-in for deploys

- Post-deploy verification: automated smoke test + watch key metrics (error rate, latency p95/p99) for a defined window before declaring a deploy "done" — don't consider a deploy successful just because the process started without crashing.
- Deploy markers/annotations on dashboards (e.g. Grafana annotation at deploy time) so a metric regression can be correlated to the deploy that caused it, not investigated blind.

## Anti-patterns to flag

- ClickOps: manual infra changes made directly in cloud console, undocumented, causing drift from IaC state
- No staging environment, or a staging environment with meaningfully different topology than prod
- Deploy = release with no gradual rollout for high-risk changes — all-or-nothing traffic cutover on unproven code
- DB migrations bundled with app code deploy with no backward-compatibility window (breaks rolling/canary deploys — see database-design.md)
- Secrets or environment-specific config hardcoded into build artifacts instead of injected at deploy time
- No automated rollback trigger — rollback only happens after a human notices something's wrong
