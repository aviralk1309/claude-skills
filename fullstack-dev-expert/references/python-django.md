# Backend — Python / Django / DRF

Use this instead of `backend.md` (Node) when the codebase/question is Django/DRF. Same
standards apply (validation at boundary, parameterized queries, auth via maintained libraries)
— specifics below are Django-flavored.

## Project structure

- App-per-domain (`orders/`, `users/`, `catalog/`) not one monolithic `app`. Each app owns its
  models, serializers, views, urls, tests.
- Settings split by environment (`settings/base.py`, `settings/prod.py`, `settings/dev.py`) —
  not one settings file with `if DEBUG` branches sprinkled through it.
- `.env` + `django-environ` (or `python-decouple`) for config — never hardcode secrets in
  settings.py.

## Django REST Framework (DRF)

- Serializers do input validation — same role Zod plays on the Node side. Validate at the
  serializer, not deep in the view/business logic.
- ViewSets + Routers for standard CRUD; plain APIView/function-based views when the endpoint
  doesn't fit REST-resource shape.
- Permission classes (`IsAuthenticated`, custom `IsOwner`) — check object-level ownership
  explicitly (`get_queryset()` filtered by `request.user`), don't rely on the URL ID alone.
- Pagination: `PageNumberPagination` for admin-style lists, cursor pagination for large/public
  feeds.

## ORM (Django ORM)

- Use `select_related`/`prefetch_related` to avoid N+1 queries — this is the Django-ORM
  equivalent of the Node `include`/`with` guidance.
- Migrations: Django's built-in migration system — always commit migration files, never edit an
  applied migration in place; make a new migration instead.
- Avoid business logic in `save()` overrides growing unbounded — prefer explicit service
  functions/model managers for complex operations, keep `save()` overrides thin.

## Async & background work

- Celery + Redis/RabbitMQ broker for background jobs (emails, report generation, webhook
  retries) — same role BullMQ plays on the Node side.
- Celery Beat for scheduled/periodic tasks.
- Idempotency keys for tasks that might be retried (payment webhooks, external API calls).

## Auth

- `django-allauth` or DRF's `SimpleJWT` for token auth — don't hand-roll password hashing
  (Django's built-in `PBKDF2`/`argon2` password hashers are already solid; don't replace them
  with something custom).
- CSRF protection stays on for session-based auth; token-based (JWT) API auth typically
  disables CSRF for those routes specifically, not globally.

## Testing

- `pytest-django` over Django's built-in `TestCase` for most teams — faster iteration, better
  fixtures ecosystem.
- Factory pattern (`factory_boy`) for test data over fixtures for anything beyond trivial
  objects.

## Deployment

- Gunicorn/Uvicorn (ASGI for async views/Django Channels) behind Nginx — not `runserver` in
  production, ever.
- `collectstatic` + a CDN or WhiteNoise for static files — don't serve static files directly
  through Django in production at scale.
- Same Docker multi-stage/non-root guidance as the Node stack applies here too.

## When to pick Django over Node for a new backend

- Team already has Python expertise (data science/ML adjacent teams especially).
- Admin panel out-of-the-box (Django Admin) saves real time for internal tools/back-office
  needs.
- Batteries-included preference (ORM, auth, admin, forms all built-in) vs. Node's
  assemble-your-own-stack philosophy — pick based on team preference, not a universal "better."