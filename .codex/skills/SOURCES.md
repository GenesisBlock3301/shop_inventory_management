# Skill Sources and Currency Register

**Last verified:** 2026-09-12

This register is the authoritative version and source record for the project-local skills. It does not authorize dependency upgrades: the architecture remains pinned to Django 5.2 LTS until an approved upgrade plan exists.

| Area | Current verified baseline | Authoritative source |
|---|---|---|
| Django | Django 5.2 LTS; official 5.2 release notes list patches through 5.2.18. Django 5.2 supports Python 3.10–3.14. | [Release notes](https://docs.djangoproject.com/en/5.2/releases/), [5.2 release](https://docs.djangoproject.com/en/5.2/releases/5.2/) |
| Django production and database work | Django 5.2 deployment checklist, checks framework, QuerySet reference, and database optimization guidance. | [Deployment](https://docs.djangoproject.com/en/5.2/howto/deployment/checklist/), [checks](https://docs.djangoproject.com/en/5.2/ref/checks/), [database optimization](https://docs.djangoproject.com/en/5.2/topics/db/optimization/) |
| Python | PEP 8 remains active guidance; project conventions override it where documented. | [PEP 8](https://peps.python.org/pep-0008/) |
| SonarQube | SonarQube Server 2026.1 LTA. Python 3.0–3.14 is fully supported. Use `sonar.python.version` to declare target Python versions. | [Quality gates](https://docs.sonarsource.com/sonarqube-server/2026.1/quality-standards-administration/managing-quality-gates/introduction-to-quality-gates), [Python analysis](https://docs.sonarsource.com/sonarqube-server/2026.1/analyzing-source-code/languages/python) |
| Application security | OWASP Top 10:2025. | [OWASP Top 10](https://owasp.org/Top10/2025/) |
| HTMX | Official documentation currently publishes HTMX 2.0.10 examples. | [HTMX documentation](https://htmx.org/docs/) |
| Alpine.js | Use current official state and directive documentation; no version-specific project dependency is locked yet. | [Alpine state](https://alpinejs.dev/essentials/state) |
| Bulma | Use current Bulma v1 documentation and its responsive, form, and table guidance. | [Bulma documentation](https://bulma.io/documentation/) |
| Accessibility | WCAG 2.2, W3C Recommendation dated 12 December 2024. | [WCAG 2.2](https://www.w3.org/TR/wcag/) |

## Refresh policy

- Review this register at least every 90 days, before dependency upgrades, and immediately after a security advisory or major framework release.
- For version-sensitive work, agents must check the linked primary source before proposing a dependency or configuration change.
- Update both this register and the affected `SKILL.md` in the same change when source guidance materially changes.
- Pin exact dependency versions in project lock files once implementation begins; do not use unpinned `latest` assets or packages in production.
