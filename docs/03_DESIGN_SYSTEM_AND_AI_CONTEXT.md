# 03 — Design System + AI Handoff Context

# Design System Direction

## Design principle
Do not start with:

> Which color looks nice?

Start with:

> What meaning should each visual treatment communicate?

For a B2B inventory/sales system, clarity and consistency matter more than decoration.

## Color approach
Most of the interface should be neutral.

Approximate guideline:
- 70–80% neutral UI
- limited primary color
- semantic colors only where meaning is required

## Initial palette

| Role | Color | Usage |
|---|---|---|
| Primary | `#2563EB` | Primary buttons, links, active nav |
| Main Text | `#111827` | Titles/body |
| Secondary Text | `#6B7280` | Metadata/helper |
| Background | `#F8FAFC` | App background |
| Surface | `#FFFFFF` | Cards/forms/tables |
| Border | `#D1D5DB` | Inputs/table boundaries |
| Success | `#166534` | Paid/completed/in stock |
| Success BG | `#DCFCE7` | Success badge background |
| Warning | `#92400E` | Partial/low stock |
| Warning BG | `#FEF3C7` | Warning background |
| Danger | `#B91C1C` | Overdue/error/delete |
| Danger BG | `#FEE2E2` | Error background |
| Info | `#1D4ED8` | Information/processing |
| Info BG | `#DBEAFE` | Information background |

## Semantic meaning
- Green = success / paid
- Amber = warning / partial / low stock
- Red = destructive / error / overdue
- Blue = action / information
- Gray = neutral

Do not reuse semantic colors decoratively.

## Do not use color alone
Combine:
- color
- text
- optionally icon

Examples:
- `✓ Paid`
- `! Due`
- `Overdue`
- `Low Stock`

## Contrast targets
Use WCAG-style targets:
- normal text ≥ 4.5:1
- large text ≥ 3:1
- important component/state boundaries around 3:1 where applicable

## Primary color usage
Use primary color for:
- primary CTA
- active nav
- links
- selected state
- focus state

Do not make everything blue.

## Design tokens
Prefer tokens instead of arbitrary hex:

```text
color.bg.canvas
color.bg.surface
color.text.primary
color.text.secondary
color.border.default

color.action.primary
color.action.primary-hover

color.status.success-text
color.status.success-bg
color.status.warning-text
color.status.warning-bg
color.status.danger-text
color.status.danger-bg
color.status.info-text
color.status.info-bg
```

## Typography hierarchy
Keep it simple:
- Page Title
- Section Heading
- Card/Table Heading
- Body
- Secondary / Metadata
- Label
- Caption

## Spacing scale
Suggested:

```text
4
8
12
16
24
32
40
48
```

Use consistently.

## Core reusable components

### Actions
- Button
- Icon Button
- Link

### Forms
- Text Input
- Number Input
- Search
- Select
- Date Picker
- Checkbox / Radio if needed

### Feedback
- Toast
- Inline Error
- Alert
- Status Badge
- Confirmation Dialog

### Navigation
- Sidebar Item
- Active State
- Breadcrumb if needed

### Data
- Table
- Pagination
- Empty State
- Filter Bar
- Summary Card

### Domain components
- Product Search Result
- Invoice Item Row
- Payment Summary
- Due Summary
- Customer Balance
- Stock Status
- Invoice Status

## Common badge states
- Paid → success
- Partial → warning
- Due → warning/danger depending on business meaning
- Overdue → danger
- Low Stock → warning
- Out of Stock → danger or strong neutral

## Table design rules
Prioritize:
- scanability
- readable row spacing
- numeric alignment
- clear labels
- useful filters only
- empty state
- minimal color noise

---

# AI Handoff Context

Use the following as compact context when asking AI to generate or review UI/UX.

## System
B2B Inventory & Sales Management System.

Primary goals:
- speed
- clarity
- low cognitive load
- reliable stock handling
- invoice/payment/due workflows
- accurate internal accounting
- simple business terminology

## Important internal models
These may exist in backend:
- `InventoryLot`
- `PaymentAllocation`
- `InvoiceItemLotAllocation`

Do not expose them directly to normal staff UI.

## User-facing concepts
Use:
- Product
- Stock
- Stock In
- Stock History
- Sale
- Invoice
- Payment
- Due
- Customer
- Customer Statement
- Return
- Reports

## Core rule

> Database complexity must not become UI complexity.

## Proposed navigation

```text
Dashboard

Inventory
- Current Stock
- Stock In
- Stock History

Sales
- New Sale
- Invoices
- Sales Return

Customers
- Customer List
- Customer Details
- Customer Statement
- Due Collection

Reports
- Sales
- Stock
- Due

Settings
```

## Core sale flow

```text
Dashboard
→ New Sale
→ Select Customer
→ Search Product
→ Select Product / Variant
→ Enter Quantity
→ Add Item
→ Review Cart
→ Payment
→ Confirm Sale
→ Invoice
→ Save / Print
```

## UX rules for AI
When generating UI/UX suggestions:
- do not expose internal model names
- do not invent missing business rules
- mark assumptions clearly
- prioritize operational efficiency over decoration
- keep daily workflows short
- prefer prevention over error correction
- preserve auditability without exposing accounting mechanics
- start with happy path, then critical edge cases
- explain why each screen/action exists
- keep visual hierarchy understandable in grayscale
- use semantic color consistently
- do not rely on color alone for state
- design for real data, empty data, error, disabled, warning, and long-content states

## Review questions for AI
For every screen:
1. What is the user trying to do?
2. Is the primary action obvious?
3. Is unnecessary information shown?
4. Is a database concept leaking into UI?
5. Can the user make a costly mistake?
6. Can the UI prevent it?
7. Is status understandable without color alone?
8. What happens on success?
9. What happens on failure?
10. Can a non-technical staff member understand the labels?
