---
name: django-production-engineering
description: Build, review, optimize, or deploy production Django applications, especially PostgreSQL-backed systems with transactional business workflows. Do not use for generic Python tasks unrelated to Django.
---

# Django Production Engineering

Use the project's supported Django and Python versions; do not invent a framework upgrade. When advice depends on a current release, CVE, deployment setting, or package behavior, browse the official Django documentation first.

## Design and implementation

- Preserve a modular-monolith boundary: models express data rules, services own multi-model business transactions, selectors own read-heavy queries, and views/forms stay thin.
- Use Django ORM and PostgreSQL constraints as the source of truth. Enforce money and quantity rules server-side with `Decimal`, database constraints, and transactions.
- For competing writes or stock/financial operations, use `transaction.atomic()` and lock the smallest required queryset with `select_for_update()`; define a deterministic lock ordering.
- Keep migrations reversible where practical, compatible with live data, and separated from data backfills when a table may be large.

## Database performance

- Measure first: inspect generated SQL and query counts before optimizing.
- Use `select_related()` for single-valued foreign keys, `prefetch_related()` for collections, and explicit projections only when they preserve the caller's needs.
- Avoid ORM work in loops, unbounded admin/report queries, accidental repeated evaluation, and template queries.
- Add indexes from real filters, joins, ordering, uniqueness, or query plans—not by habit. Use PostgreSQL `EXPLAIN (ANALYZE, BUFFERS)` for consequential queries.

## Production readiness

- Separate development and production settings; secrets must be environment-managed and never committed.
- Run `python manage.py check --deploy`, migration checks, tests, static-file collection, and a restore-tested database-backup process before release.
- Configure structured application logging, safe error reporting, health checks, trusted hosts/proxy HTTPS behavior, secure cookies, and a non-debug production configuration.

Authoritative starting points: Django's deployment checklist, system checks, performance guide, QuerySet reference, and PostgreSQL documentation. Refresh these sources when a decision is version-sensitive.
