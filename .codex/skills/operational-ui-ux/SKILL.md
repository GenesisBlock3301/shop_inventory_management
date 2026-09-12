---
name: operational-ui-ux
description: Design or review accessible, high-efficiency UI/UX for internal operational web applications such as inventory, sales, finance, and administration. Do not use for brand-only visual exploration.
---

# Operational UI/UX

Prioritize accurate, fast, low-training daily operation over decoration.

- Begin with the actor, the job to complete, the decision they must make, and the costly mistake the interface must prevent.
- Make the primary next action visible; keep secondary, destructive, and privileged actions distinct. Hide or disable unavailable actions with an explanation where useful.
- Design every workflow for loading, empty, validation-error, permission-denied, failed-save, duplicate-submission, success, and long-content states.
- Use plain domain language; never surface storage or accounting implementation names merely because they exist in the database.
- Use semantic color with text/icon labels, keyboard-accessible focus states, clear form labels/errors, sufficient contrast, responsive layouts, and sensible tab order.
- For data tables, support practical search/filtering, stable column hierarchy, numeric alignment, totals where needed, pagination, and export/print only when it serves a real task.
- For financial or stock commits, provide a concise review state, show the effect on due/stock where relevant, make submits idempotent, and show an immutable reference after success.

When current accessibility standards or component behavior matters, verify against WCAG and the chosen component framework's official documentation.
