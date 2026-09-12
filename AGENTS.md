# Project Engineering Constitution

This file is binding for Codex and any AI coding assistant that operates in this repository. Read it before planning, reviewing, or changing files. Direct user instructions remain higher priority.

## Mandatory project skills

The canonical project skill library is [`.codex/skills/`](.codex/skills/). Before work begins, read the `SKILL.md` for every skill matching the task:

| Work | Required skill(s) |
|---|---|
| Django, PostgreSQL, migrations, transactions, production operations | `django-production-engineering` |
| Python design, clean code, tests, architecture | `python-quality-architecture` |
| HTMX, Alpine.js, Django templates, partial responses | `django-htmx-alpine-frontend` |
| Bulma, responsive templates, accessibility | `bulma-accessible-ui`, `operational-ui-ux` |
| Browser workflow and accessibility testing | `frontend-workflow-testing` |
| Authentication, authorization, secrets, input/output, dependencies | `web-security-owasp`, `security-best-practices` |
| SonarQube setup, findings, rule profile, quality gate, hotspots | `sonarqube-quality-gate` |

Do not substitute a different application architecture without explicit approval. This project is a Django modular monolith with Django Templates, HTMX, Alpine.js, Bulma, and PostgreSQL.

For version-sensitive work, also read [`.codex/skills/SOURCES.md`](.codex/skills/SOURCES.md). It records the official sources, verified versions, and mandatory 90-day refresh process.

## Required engineering practices

- Treat the PRD, architecture, schema, and requirements-lock document in `docs/` as the product source of truth.
- Keep business rules server-side. UI code can preview values but cannot authorize prices, quantities, totals, stock, or payments.
- Use `Decimal` for money; use PostgreSQL transactions and row locks for inventory and financial commits.
- Make changes narrowly, preserve existing user work, and add or update tests with every behavior change.
- For any user-facing workflow, cover success, validation failure, authorization denial, duplicate submission, and error states proportionately to risk.
- Do not commit secrets, production credentials, generated coverage, or scanner caches.

## Quality and security gate

New code must meet these merge conditions once CI is present:

- No new SonarQube vulnerabilities.
- No unresolved high-severity new reliability or security issues.
- Every new security hotspot is reviewed and its disposition documented.
- New-code test coverage is at least 80%; duplicated new-code lines are at most 3%.
- Django tests, formatting/linting, type checks, and `python manage.py check --deploy` (for production configuration) pass when applicable.

Do not suppress a SonarQube finding without documenting why it is a verified false positive or accepted risk. Quality gates apply to **new code**, not as an excuse to block initial delivery on unrelated legacy backlog.
