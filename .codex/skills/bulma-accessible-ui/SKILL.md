---
name: bulma-accessible-ui
description: Build or review Bulma-based, responsive, accessible UI for this project's operational web application. Use for templates, forms, tables, components, visual tokens, and accessibility remediation.
---

# Bulma Accessible UI

Use semantic HTML first and Bulma classes for layout and styling. Do not replace semantic controls with visually similar generic elements.

## Components and layout

- Reuse the project's visual tokens, spacing scale, semantic status colors, and existing components. Use color to reinforce a state, never as its only signal.
- Pair every input with a visible label; attach useful help and error text programmatically. Preserve entered values when server validation fails.
- Use real tables for tabular business data, including meaningful header cells and accessible row actions. Wrap wide tables in Bulma's `table-container`; do not compress essential accounting values into unreadable cards by default.
- Keep primary actions prominent and destructive actions visually distinct with confirmation where consequences are material.
- Build mobile-responsive layouts without hiding operationally necessary data; offer an intentional compact representation or horizontal table scrolling.

## Accessibility baseline

- Target WCAG 2.2 AA: keyboard access, visible non-obscured focus, readable contrast, 24-by-24 CSS-pixel minimum pointer targets where applicable, clear error identification, and status messages that assistive technology can announce.
- Use native buttons, links, checkboxes, radios, and dialogs when possible. For a custom modal, manage focus, Escape dismissal where appropriate, backdrop behavior, and focus return.
- Do not remove browser focus outlines without replacing them with an equally visible focus treatment.

Use the current official Bulma and W3C WCAG documentation for version-sensitive implementation details.
