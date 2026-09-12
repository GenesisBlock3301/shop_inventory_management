---
name: web-security-owasp
description: Design, implement, or review web-application security controls using OWASP Top 10 and framework-specific guidance. Use for security-sensitive Django work and explicit security reviews, not routine styling or feature work.
---

# Web Security OWASP

Use the current OWASP Top 10 release and official framework documentation when a requirement is security-sensitive; do not rely on an older Top 10 by default. Treat the Top 10 as a risk taxonomy, then derive testable controls for the application.

## Required review areas

- **Access control:** enforce object-level and action-level authorization on the server; hidden UI is never authorization. Test horizontal and vertical privilege escalation.
- **Configuration and secrets:** production `DEBUG` off, explicit hosts and trusted origins, secure cookies and HTTPS at the correct proxy boundary, no secrets in source/logs, restricted admin access.
- **Authentication and sessions:** use framework password hashing and CSRF protections; protect login, reset, session expiry, and rate-limited abuse paths.
- **Input and output:** use parameterized ORM queries, validate server-side, encode output by context, validate uploads, and prevent unsafe redirects and SSRF.
- **Data, crypto, and logging:** minimize sensitive data; use modern library defaults; record security-relevant events without logging credentials, tokens, or payment data.
- **Dependencies and integrity:** pin dependencies appropriately, review advisories, validate CI/CD and release artifacts, and keep security patches traceable.
- **Resilience:** use safe errors, timeouts, size limits, idempotency for financial mutations, and failure handling that preserves integrity.

For an audit, state threat, affected boundary, evidence, severity, remediation, and a regression test. Do not claim compliance without evidence.
