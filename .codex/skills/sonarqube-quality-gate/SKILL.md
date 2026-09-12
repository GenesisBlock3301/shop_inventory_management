---
name: sonarqube-quality-gate
description: Configure, operate, or review this project's SonarQube analysis, quality gate, quality profile, and security-hotspot workflow. Use for CI, scanner configuration, findings triage, and merge-quality decisions.
---

# SonarQube Quality Gate

Use SonarQube as an enforceable new-code quality gate, not a substitute for tests or a way to accumulate undocumented suppressions.

## Project policy

- Start with the current built-in **Sonar way** profile for Python. Any rule override must identify the reason, owner, and review date in repository documentation.
- Analyze pull requests and the main branch. Set the supported Python version explicitly using `sonar.python.version`.
- Fail new code with any vulnerability, unresolved high-severity reliability/security issue, new-code coverage below 80%, or duplicated new-code lines above 3%.
- Security hotspots require human review and a documented disposition before merge; they are not automatically equivalent to vulnerabilities.
- Keep scanner tokens and server URLs in protected CI secrets. Never commit them to `sonar-project.properties`, `pyproject.toml`, or source code.

## Setup and triage

1. Confirm whether analysis targets SonarQube Server or SonarQube Cloud, then obtain its URL/project key/token through approved secret management.
2. Configure tests and coverage before enforcing coverage thresholds. Exclude generated, vendored, and migration artifacts deliberately—not production source to improve metrics.
3. Triage findings by root cause. Fix the source issue where possible; do not bulk-mark issues as false positive.
4. Report the gate result, findings by quality category, security-hotspot review status, and any justified exception.

For scanner syntax, supported analysis properties, or current quality-gate behavior, verify against current SonarSource documentation before implementation.

For the current verified source/version baseline, read [the project skills register](../SOURCES.md).
