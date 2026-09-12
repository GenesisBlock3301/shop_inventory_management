---
name: frontend-workflow-testing
description: Test and review critical server-rendered frontend workflows for usability, accessibility, and failure resilience in this inventory and sales application. Use for validating UI changes and regression coverage, not backend unit tests alone.
---

# Frontend Workflow Testing

Validate user outcomes, not only markup or HTTP status codes. Prefer browser-level tests for behavior spanning Django templates, HTMX, and Alpine.js.

## Required workflow coverage

- Exercise login and role-specific navigation.
- Cover Stock In, product/customer search, multi-item sale, insufficient-stock rejection, partial payment, due collection, invoice print view, and customer statement.
- For each workflow, verify normal completion plus empty, invalid, unauthorized, double-submit, network/server failure, and long-content states where applicable.
- Assert user-visible results, server-confirmed values, focus behavior after HTMX swaps, and that a refresh does not repeat a committed financial or stock action.

## Accessibility and responsive checks

- Test keyboard-only completion of critical flows, visible focus, labels/errors, modal focus management, and status announcements.
- Check narrow and standard desktop viewports; tables must remain usable and numeric columns legible.
- Test with JavaScript unavailable for pages that claim progressive enhancement; normal links and forms must still reach their server-rendered outcomes.

Keep fixtures realistic and deterministic. A defect fix must add a regression test at the smallest layer that proves the user-visible failure is resolved.
