# 02 — User Flows + Wireframe / UX Rules

## Recommended design process

```text
Final PRD
→ Actors + User Jobs
→ Information Architecture
→ Happy-path User Flows
→ Low-fi Wireframes
→ Critical Edge Cases
→ Usability Review
→ High-fi UI
→ Components / Design System
→ Clickable Prototype
→ User Testing
→ UX / PRD Adjustments
→ Development
```

Do not jump directly from PRD to polished UI.

---

# Core User Flows

## 1. Login

```text
Login
  ↓
Valid?
 ├─ Yes → Dashboard
 └─ No  → Error → Retry
```

## 2. New Sale

```text
Dashboard
  ↓
New Sale
  ↓
Select / Search Customer
  ↓
Search Product
  ↓
Select Product / Variant / Pack
  ↓
Enter Quantity
  ↓
Add Item
  ↓
Add More?
 ├─ Yes → Search Product
 └─ No  → Checkout
             ↓
          Payment
             ↓
        Confirm Sale
             ↓
           Invoice
             ↓
        Save / Print
```

## 3. Full Payment

```text
Invoice Total = 10,000
Received = 10,000
  ↓
Status = Paid
```

Internal payment allocation should be automatic.

## 4. Partial Payment

```text
Invoice Total = 10,000
Received = 7,000
Paid = 7,000
Due = 3,000
  ↓
Status = Partial / Due
```

Remaining due must be visually obvious.

## 5. Due Collection

```text
Customers
  ↓
Select Customer
  ↓
Outstanding Due
  ↓
Receive Payment
  ↓
Enter Amount
  ↓
Confirm
  ↓
Updated Balance
```

Example:

```text
Due = 3,000
Received = 2,000
Remaining = 1,000
```

## 6. Customer Statement

```text
12 Sep   Invoice #102       +10,000
12 Sep   Cash Received       -7,000
15 Sep   Cash Received       -2,000
-----------------------------------
Current Due                  1,000
```

## 7. Stock In

```text
Inventory
  ↓
Stock In
  ↓
Select Product
  ↓
Select Variant / Pack if applicable
  ↓
Enter Quantity
  ↓
Enter cost/batch fields if required
  ↓
Confirm
  ↓
Stock Updated
```

Backend may create an `InventoryLot` automatically.

## 8. Current Stock

```text
Inventory
  ↓
Current Stock
  ↓
Search / Filter
  ↓
View Product
  ↓
See Available Quantity
```

## 9. Stock History

```text
Inventory
  ↓
Stock History
  ↓
Filter by product/date/type
  ↓
View Movement
```

Use human-readable movement types:
- Stock In
- Sale
- Return
- Adjustment

## 10. Sales Return

```text
Invoices
  ↓
Open Invoice
  ↓
Return Item
  ↓
Select Item + Quantity
  ↓
Validate
  ↓
Review Financial Impact
  ↓
Confirm Return
  ↓
Stock / Financial Records Updated
```

Internal lot allocation may be used for accurate reversal.

---

# Critical Edge Cases

## Insufficient Stock

```text
Available = 3
Entered Qty = 5
  ↓
Block
  ↓
"Only 3 units available"
```

## Product Not Found
Show:
- clear empty state
- retry search
- clear filters
- create product only if user has permission

## Payment Validation
Handle:
- negative amount
- invalid number
- partial payment
- exact payment
- overpayment according to PRD

Do not invent business rules.

## Other important states
Design for:
- empty tables
- failed save
- duplicate submission prevention
- permission denied
- return validation
- stock adjustment permission
- overdue due

---

# Wireframe Rules

## What wireframe means
A wireframe is a low-fidelity structural blueprint showing:
- information
- layout
- actions
- interaction sequence

It is not about:
- final colors
- polished typography
- decoration
- animation

## Happy-path wireframe
Start with the successful main flow only.

Example:

```text
Dashboard
→ New Sale
→ Add Product
→ Quantity
→ Payment
→ Confirm
→ Invoice
```

Then add edge cases.

## Grayscale first
Initial wireframes should be grayscale.

Avoid:
- gradients
- branding obsession
- decorative colors
- polished visuals too early

If the interface works in grayscale, hierarchy is likely sound.

## New Sale low-fi example

```text
┌─────────────────────────────────────────────────────┐
│ New Sale                                      User  │
├───────────────┬─────────────────────────────────────┤
│ Dashboard     │ Customer                            │
│ Inventory     │ [ Search customer...           ]    │
│ Sales         │                                     │
│ Customers     │ Product                             │
│ Reports       │ [ Search product...            ]    │
│               │                                     │
│               │ Items                               │
│               │ ┌─────────────────────────────────┐ │
│               │ │ Product   Pack  Qty  Rate Total │ │
│               │ │ LED Bulb   6pc   2    600  1200 │ │
│               │ └─────────────────────────────────┘ │
│               │                                     │
│               │                     Total: 1,200     │
│               │                                     │
│               │ Payment                             │
│               │ [ Cash ▼ ]  Received [ 1000 ]      │
│               │                     Due: 200         │
│               │                                     │
│               │ [ Save Invoice ] [ Save & Print ]   │
└───────────────┴─────────────────────────────────────┘
```

## UX review questions
For every screen ask:
1. What is the user's main goal?
2. What is the primary action?
3. What information is required?
4. What can be hidden?
5. What mistakes are likely?
6. Can the UI prevent those mistakes?
7. Is terminology business-friendly?
8. Is any DB concept leaking?
9. What happens after success?
10. How does the user recover from failure?

## Operational UX principles
- minimize cognitive load
- prevent errors before submission
- keep one clear primary action
- use explicit destructive labels
- use progressive disclosure
- preserve user context
- avoid exposing backend concepts

## Usability test example
Give the user this task:

> A customer buys 2 packs. Total is 1,200. They pay 1,000. Complete the sale and show the remaining due.

Observe:
- hesitation
- misunderstood labels
- missed buttons
- likely mistakes
- missing information
