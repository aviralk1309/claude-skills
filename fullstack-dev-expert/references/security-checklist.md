# Security Checklist — Non-Negotiable Defaults

Quick-reference. Flag any of these when missing from code the user shares, even if unasked —
one line, then continue with the requested task.

## Every API
- [ ] Security headers set (Helmet.js or Nest equivalent)
- [ ] Rate limiting on public endpoints, stricter on auth endpoints
- [ ] CORS locked to known origins in production (never `*` with credentials)
- [ ] Input validated at every trust boundary (body/query/params/headers)
- [ ] Errors don't leak internal details (stack traces, DB errors) to clients

## Auth
- [ ] Passwords hashed with argon2/bcrypt — never plaintext, never reversible encryption
- [ ] JWTs short-lived; refresh tokens rotated; stored httpOnly/secure, not localStorage
- [ ] Authorization re-checked server-side on every request — never trust a client-supplied ID
      or role claim without verifying against the actual record

## Database
- [ ] Parameterized queries only — no string-concatenated SQL, ever
- [ ] Least-privilege DB user for the app (not a superuser connection string)
- [ ] Secrets (connection strings, API keys) in env vars, never committed

## Frontend
- [ ] No secrets/API keys in client-side bundle (anything in `NEXT_PUBLIC_*` is public)
- [ ] CSP configured for anything rendering user-generated content
- [ ] Sanitize any HTML rendered via `dangerouslySetInnerHTML`

## Infra
- [ ] Containers run as non-root
- [ ] Dependencies scanned for known vulnerabilities (`npm audit`, Dependabot/Snyk)
- [ ] Backups exist and restore has actually been tested at least once