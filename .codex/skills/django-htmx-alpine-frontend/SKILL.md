---
name: django-htmx-alpine-frontend
description: Build or review this project's Django Template, HTMX, and Alpine.js frontend interactions. Use for server-rendered forms, partial updates, search, modals, and invoice-like dynamic workflows; do not use for a separate SPA architecture.
---

# Django Templates, HTMX, and Alpine.js

Preserve the locked frontend architecture: Django renders authoritative HTML, HTMX performs server-driven partial updates, and Alpine.js owns only small browser-local state.

## Interaction design

- Start with a working server-rendered URL and form. Add HTMX as progressive enhancement; non-JavaScript submission must still produce a useful result where the operation is practical without JavaScript.
- Put business validation, prices, stock, totals, permissions, and commits on the server. Alpine previews may improve responsiveness but never become authoritative.
- Use explicit HTMX targets and swap behavior. Return a focused fragment for an HTMX request and a complete page for a normal request; keep identifiers stable.
- Display loading, empty, validation-error, failed-request, and success states. Prevent duplicate submits by disabling the submit affordance while a request is in flight, but retain backend idempotency.
- Use Alpine `x-data` locally for transient UI state—row display, modal visibility, dropdowns, and live previews. Avoid global stores and duplicated domain state unless a real cross-page need exists.

## Operational workflows

- Make product/customer search keyboard-friendly and debounced without preventing an ordinary form search.
- On invoice, payment, stock, or other irreversible commits, show the server-confirmed record number and values after success; do not rely on a client calculation as a receipt.
- Maintain focus after swaps and expose status updates through an appropriate live region when the user needs confirmation.

Refresh guidance against official HTMX and Alpine.js documentation when their behavior or versions matter.
