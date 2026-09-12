---
name: python-quality-architecture
description: Improve Python code quality, maintainable architecture, testability, and SonarQube integration for production applications. Do not use for one-off scripts where this level of structure is unnecessary.
---

# Python Quality Architecture

Aim for readable, cohesive, testable code rather than abstract patterns for their own sake.

## Clean Python

- Keep functions small enough to expose one intent; name business concepts precisely; prefer straightforward control flow over cleverness.
- Model invalid states out where reasonable. Validate at input boundaries and keep domain rules close to the transaction that depends on them.
- Use type hints for public/internal service boundaries, `Decimal` for money, explicit timezone-aware datetimes, and narrow exception handling that preserves context.
- Avoid duplicate business rules, hidden global state, boolean-flag APIs, premature frameworks, and `except Exception` recovery that silently changes outcomes.

## Architecture and testing

- Organize by domain, with directional dependencies: presentation → application service → domain/data access. Avoid circular imports and cross-app model reach-through.
- Test business invariants and permission boundaries at the service level; use integration tests for transactions, locks, queries, migrations, and critical external boundaries.
- Keep tests deterministic with factories/fixtures that represent real business cases. Add a regression test before fixing a defect.

## SonarQube

- Run analysis in CI and enforce a quality gate for new code. Use the current built-in **Sonar way** profile unless a documented project rule needs adjustment.
- Set the supported Python version (`sonar.python.version`), provide coverage and test-report paths, exclude generated/vendor artifacts deliberately, and fix root causes rather than bulk-suppressing findings.
- Treat security hotspots as manual-review items and document the review decision. For current scanner or server behavior, verify against SonarSource documentation before configuring it.

For the current verified source/version baseline, read [the project skills register](../SOURCES.md).
