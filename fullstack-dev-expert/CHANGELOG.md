# Changelog

All notable changes to this project are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]
- Awaiting further eval expansion / additional stack references (Java/Spring, PHP/Laravel, Go)

## [0.2.0] — 2026-07-05
### Added
- Python/Django ecosystem support (`references/python-django.md`): DRF, ORM, Celery, auth,
  testing, deployment guidance.
- `references/org-context.md`: product-based vs. service/consulting-based decision differences
  (architecture, documentation depth, tooling, scope discipline).
- Node vs. Python stack-detection routing in `SKILL.md`.
- 2 new eval cases (Django IDOR fix, agency/handoff architecture decision) — 7/7 passing.

### Changed
- `SKILL.md` frontmatter `description` broadened to cover both ecosystems and
  product-vs-service triggers; trimmed to stay under the 1024-character skill limit.

## [0.1.0] — 2026-07-05
### Added
- Initial skill release: `SKILL.md` with routing, tech-stack defaults, code standards, and
  task-type playbooks (explain / build / refactor / debug / performance).
- Reference files: `tech-stack.md`, `frontend.md`, `backend.md`, `database.md`, `devops.md`,
  `security-checklist.md`.
- `evals/evals.json` with 5 initial test cases — 5/5 passing.
- Packaged and validated as `fullstack-dev-expert.skill`.