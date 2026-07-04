# Authentication & Authorization

## Authentication (who are you)

- **Never roll your own crypto/password storage.** Use a vetted library. Passwords: hash with bcrypt/argon2/scrypt (salted, slow by design) — never plain hash (MD5/SHA-256 alone), never plaintext.
- **Sessions vs tokens**:
  - Server-side sessions (session ID in cookie, state in DB/Redis): easy to revoke instantly, but needs shared session store across instances.
  - JWT/stateless tokens: no server-side lookup needed (good for scale/microservices), but **hard to revoke before expiry** — mitigate with short expiry + refresh tokens, or a token blocklist if early revocation is a hard requirement.
  - Default: sessions for traditional web apps; tokens for APIs/microservices/mobile where a shared session store is awkward.
- **OAuth2/OIDC** for "login with X" and delegated access — OAuth2 is authorization (access delegation), OIDC layers identity/authentication on top. Don't build custom SSO if OIDC covers the need.
- **MFA** for anything sensitive — TOTP or WebAuthn/passkeys over SMS (SMS is phishable/SIM-swappable).
- Cookies: `HttpOnly` + `Secure` + `SameSite=Lax/Strict` to mitigate XSS token theft and CSRF (see `security-appsec.md` CSRF section for token-based mitigation when SameSite alone isn't sufficient).

## Authorization (what can you do)

| Model | Use when |
|---|---|
| RBAC (role-based) | Permissions map cleanly to a small set of roles (admin/editor/viewer) |
| ABAC (attribute-based) | Access depends on runtime attributes (own resource, department, time of day) — more flexible, more complex |
| ReBAC (relationship-based) | Permission depends on a graph relationship (shared docs, org hierarchy) — e.g. Google Docs sharing, Zanzibar-style |

Start with RBAC; move to ABAC/ReBAC only when roles alone can't express the actual rule ("can edit only their own posts" is already past pure RBAC).

## Where to enforce

- **AuthN at the edge/gateway** — verify identity once, pass a verified identity (claims) downstream, don't re-implement login logic in every service.
- **AuthZ close to the resource** — the service owning the data makes the final access decision, since it has the context (ownership, state). Coarse-grained checks (is this a valid user at all) can happen at the gateway; fine-grained checks (can *this* user edit *this* resource) belong in the service.
- Centralize policy logic where possible (a shared policy library, or a dedicated authz service like OPA) rather than reimplementing the same rule in five services — avoids drift where one service's copy of the rule goes stale.

## API-to-API (service-to-service) auth

- **mTLS** or **service-to-service tokens** (e.g. via a service mesh or signed short-lived JWTs) between internal services — don't leave internal APIs unauthenticated just because they're "internal," and don't reuse end-user tokens for service calls that need broader/different scope.
- API keys for external partner integrations: scoped, rotatable, rate-limited per key, never a single shared key across all partners.

## Common mistakes to flag

- Storing JWTs in `localStorage` (vulnerable to XSS exfiltration) instead of `HttpOnly` cookies
- No token expiry, or expiry so long it defeats the point
- Authorization checks only in the UI/frontend, trusting the client — always re-check server-side
- Overly broad token scopes ("this token can do everything") instead of least-privilege scoping
- Secrets (API keys, DB creds) committed to source control or baked into images instead of a secrets manager
