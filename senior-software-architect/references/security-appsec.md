# Security / AppSec Hardening

(AuthN/AuthZ specifics live in `auth.md` — this file covers everything else: threat modeling, secrets, supply chain, injection classes, transport/data security.)

## Threat modeling — do it once per system, revisit per major change

**STRIDE**, applied per trust boundary (client↔API, service↔service, service↔DB):

| Threat | Question to ask | Typical mitigation |
|---|---|---|
| Spoofing | Can someone pretend to be a legit user/service? | Strong authN, mTLS between services |
| Tampering | Can data be modified in transit/at rest without detection? | TLS, signed payloads, DB integrity constraints |
| Repudiation | Can an action be denied after the fact? | Audit logging, non-reputable action records |
| Information disclosure | Can data leak to someone unauthorized? | Encryption, least-privilege access, no verbose errors |
| Denial of service | Can availability be knocked out? | Rate limiting, circuit breakers (see microservices.md) |
| Elevation of privilege | Can a low-priv actor gain more access? | AuthZ checks server-side, input validation |

Draw the data flow, mark trust boundaries, ask all six questions at each boundary. Skip this for internal prototypes; mandatory before a system handles real user data or payments.

## OWASP Top 10 classes — the ones that recur most in review

- **Injection (SQL/NoSQL/command)**: parameterized queries / prepared statements always, never string-concatenated input into a query or shell command. ORMs don't automatically save you if raw query escapes are used.
- **Broken access control**: server-side authZ check on every resource access, keyed by the resource's actual owner — never trust a client-supplied ID/role. (Full detail in auth.md.)
- **SSRF**: if the server fetches a URL supplied by user input (webhooks, "import from URL" features), validate/allowlist destination, block internal/private IP ranges (169.254.x.x, 10.x, 192.168.x, cloud metadata endpoints like 169.254.169.254) — a naive fetch-any-URL feature is a direct path to internal network/cloud credentials.
- **Deserialization of untrusted input**: avoid deserializing arbitrary object graphs from user input (`pickle` in Python, unrestricted `ObjectInputStream` in Java) — prefer plain data formats (JSON) with schema validation.
- **XSS**: escape/encode output by context (HTML/attribute/JS/URL), rely on framework auto-escaping (React/Vue templates) rather than manual string building, `Content-Security-Policy` header as defense-in-depth.
- **CSRF**: `SameSite` cookies (see auth.md) + CSRF tokens for state-changing requests if using cookie-based sessions; less relevant for pure token-in-header APIs.
- **Security misconfiguration**: default credentials changed, unused endpoints/debug routes disabled in prod, verbose stack traces never returned to the client (see api-design.md error shape).

## Secrets management

- Never in source control — no exception for "it's a private repo" or "it's just a dev key." Use a secrets manager (Vault, AWS/GCP/Azure secrets manager) or at minimum env vars injected at deploy time, not baked into images.
- Rotate on a schedule and immediately on suspected exposure (accidental commit, departing employee with access, third-party breach). Rotation should be a runbook, not a fire drill — if rotating a DB credential requires downtime, that's a design gap to fix ahead of time, not during an incident.
- Scope secrets narrowly: a service gets only the credentials it needs (its own DB, not the whole cluster's). Blast radius of one leaked secret should be one service, not the system.
- Scan for accidentally committed secrets pre-commit (gitleaks, truffleHog) and in CI as a hard gate, not just local hook that can be skipped.

## Supply chain / dependency security

- **SCA (software composition analysis)**: scan dependencies for known CVEs on every build (Dependabot, Snyk, `npm audit`/`pip-audit`) — most real-world breaches exploit a known vulnerability in a dependency, not a novel zero-day.
- Pin dependency versions (lockfiles committed) — don't float on `^`/`latest` in production; reproducible builds matter for both stability and security auditability.
- Verify package integrity (checksums/lockfile hashes) — protects against a compromised registry serving a tampered package under a legitimate name.
- Least-privilege CI: a CI pipeline that can publish a release shouldn't also have prod DB credentials in the same job/token scope — compromise of one shouldn't compromise the other (relates to bulkhead isolation, microservices.md).
- SBOM (Software Bill of Materials) generation for anything with compliance requirements or that ships to enterprise customers — increasingly a contractual/regulatory expectation, not optional polish.

## Data protection

- **Encryption in transit**: TLS everywhere, including internal service-to-service traffic (not just the public edge) — "internal network is trusted" is not a valid assumption inside a cloud VPC shared with other tenants.
- **Encryption at rest**: DB-level encryption for anything sensitive (PII, payment data, health data) at minimum; field-level encryption for the most sensitive fields if the threat model requires protecting data even from someone with raw DB access.
- **PII minimization**: collect and retain only what's actually needed, for only as long as needed — the cheapest way to reduce breach impact is not having the data in the first place. Relevant to GDPR/CCPA-style compliance regardless of which specific regime applies.
- **Logging discipline**: never log secrets, full payment card numbers, passwords, or full auth tokens — mask/redact before logging. A leaked log stream is as damaging as a leaked DB in practice.

## Secure SDLC — where security fits in the pipeline

1. Threat model at design time (before code, for anything non-trivial)
2. Static analysis (SAST) + dependency scan (SCA) on every PR, gating merge on high/critical findings
3. Secrets scan pre-commit and in CI
4. Dynamic analysis (DAST) / penetration testing pre-release for public-facing systems handling sensitive data
5. Runtime monitoring: anomaly detection on auth failures, unusual access patterns, WAF for public endpoints

Security review, like code review, ranks issues by blast radius: an authZ bypass or secret leak outranks a missing `Content-Security-Policy` header — say so explicitly when triaging a list of findings, don't present them as a flat list.

## Anti-patterns to flag

- "We'll add security later" on anything already handling real user data — retrofitting authZ/encryption is far more expensive than designing it in
- Security through obscurity (hidden/unlisted endpoint instead of actual authZ check)
- Client-side-only validation with no server-side re-check
- One shared API key/credential for all partners/tenants instead of scoped-per-tenant credentials
- Security decisions made without a threat model — "we added encryption" without knowing what threat it addresses often misses the actual risk (e.g. encrypting at rest but not restricting who has DB access at all)
