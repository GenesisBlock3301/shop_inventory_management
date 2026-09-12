# Requirements Lock & Foundation Engineering Plan

## 1. Purpose

This document resolves the remaining ambiguities between the PRD, system architecture, database schema, and UI flow before implementation begins.

**Project:** Inventory & Sales Management System  
**Initial Client:** Electrical Shop  
**V1 Budget:** BDT 25,000  
**Architecture:** Django Modular Monolith  
**Database:** PostgreSQL  
**Target Users:** Owner/Admin + approximately 3–4 Staff

The objective is to keep V1:

- Simple for real shop users
- Correct for stock, sales, payment and due
- Protected against unauthorized manipulation
- Affordable to develop and host
- Reusable for another similar shop through separate deployment

---

# 2. Canonical Inventory Model

## Decision

Use:

```text
Product
→ ProductVariant
→ InventoryLot
→ StockMovement
```

**`ProductBatch` will not be used as a separate model.**

`InventoryLot` is the single canonical stock-cost layer.

---

## Why InventoryLot

A physical batch number is not available or useful for every product.

Example:

```text
LED Bulb 12W
```

may arrive without any meaningful batch number.

But the system still needs to know:

```text
50 pcs received @ ৳180
100 pcs received @ ৳195
```

Therefore:

```text
InventoryLot
```

always exists internally.

An external batch number is simply optional information on the lot.

Example:

```text
InventoryLot

Variant:
LED 12W

External Batch:
NULL

Quantity:
50

Unit Cost:
৳180
```

Another product may have:

```text
External Batch:
B-2026-105
```

---

# 3. Stock-In Model

A user-facing stock posting will be represented by:

```text
StockEntry
→ InventoryLot
→ StockMovement
```

## StockEntry

Represents one stock-in operation.

Example:

```text
STK-000001

Date:
12 Sep 2026

Created By:
Rafiq
```

A Stock Entry can contain one or more product variants.

---

## InventoryLot

Each stock line creates an InventoryLot containing:

```text
variant
optional external batch
quantity received
quantity remaining
unit cost
received date
```

---

## StockMovement

The ledger records:

```text
STOCK_IN    +50
SALE        -10
REVERSAL    +10
```

Users do not need to understand these tables.

UI simply shows:

```text
Previous Stock: 100
Added:            50
Current Stock:   150
```

---

# 4. FIFO Stock Consumption Rule

## Default Rule

V1 will use:

**FIFO — First In, First Out**

For a non-batch-tracked product:

```text
Oldest available InventoryLot
→ consumed first
```

Example:

```text
Lot A
20 pcs @ ৳180
Received Jan 01

Lot B
30 pcs @ ৳195
Received Feb 01
```

Selling 25 pieces consumes:

```text
20 from Lot A
+
5 from Lot B
```

---

## Batch-Tracked Product

If a variant requires batch tracking:

1. Staff selects the batch in the invoice.
2. Backend finds available InventoryLots belonging to that batch.
3. FIFO applies within that selected batch.

This preserves batch control without complicating normal products.

---

# 5. Product Pricing Model

`ProductVariant` may contain:

```text
default_cost_price
default_sale_price
minimum_sale_price
```

## Meaning

### default_cost_price

Convenience value used to prefill Stock In.

It is **not the historical accounting cost**.

Actual stock cost comes from:

```text
InventoryLot.unit_cost
```

### default_sale_price

Suggested/list selling price.

Example:

```text
৳220
```

### minimum_sale_price

Lowest normal price Staff may use.

Example:

```text
৳190
```

---

## Negotiated Price

Staff may change the actual selling price during invoice creation.

Example:

```text
Suggested:  ৳220
Actual:     ৳205
```

The actual historical sale price is stored in:

```text
InvoiceItem.unit_price
```

Profit calculations use:

```text
InvoiceItem.unit_price
-
actual InventoryLot cost
```

not `default_sale_price`.

---

# 6. Canonical Invoice States

Invoice has only one persisted lifecycle status:

```text
DRAFT
POSTED
VOID
```

## DRAFT

- Can be edited.
- Does not reduce stock.
- Is not part of financial reports.

## POSTED

- Official sale.
- Stock has been deducted.
- Cannot be freely edited.

## VOID

- Transaction has been cancelled through controlled reversal.
- Remains visible in history.

---

# 7. Payment Status Is Derived

The following are **not Invoice lifecycle states**:

```text
UNPAID
PARTIAL
PAID
```

They are derived payment states.

For a POSTED invoice:

```text
Allocated Payment = 0
→ UNPAID
```

```text
0 < Allocated Payment < Invoice Total
→ PARTIAL
```

```text
Allocated Payment = Invoice Total
→ PAID
```

No separate editable payment-status field is required.

This prevents lifecycle status and payment status from becoming inconsistent.

---

# 8. Canonical Payment Model

All payments use:

```text
Customer
→ Payment
→ PaymentAllocation
→ Invoice
```

## Decision

`Payment` will **not** contain a direct `invoice_id`.

---

## Payment

Represents actual money received.

Example:

```text
Payment:
৳10,000

Customer:
Rahman Electric
```

---

## PaymentAllocation

Explains where that money was applied.

Example:

```text
Payment ৳10,000

INV-001 → ৳6,000
INV-002 → ৳4,000
```

---

# 9. Payment Allocation Rule

## Payment During New Sale

If payment is received while creating an invoice:

```text
Payment
→ allocated directly to that Invoice
```

Example:

```text
Invoice:
৳15,000

Paid:
৳10,000

Due:
৳5,000
```

---

## Generic Due Collection

When Staff chooses:

```text
Receive Payment
→ Customer
```

V1 automatically allocates payment against the customer's **oldest outstanding POSTED invoices first**.

Example:

```text
INV-001 Due = ৳3,000
INV-002 Due = ৳5,000

Customer pays:
৳4,000
```

Allocation:

```text
INV-001 → ৳3,000
INV-002 → ৳1,000
```

Remaining:

```text
INV-002 → ৳4,000
```

Staff does not need to manually manage PaymentAllocation.

This reduces UI complexity.

---

# 10. Money and Quantity Precision

## Currency

V1 currency:

```text
BDT
```

Business settings may retain a configurable currency code for future reuse, but V1 is designed for Bangladesh.

---

## Money

Use:

```text
NUMERIC(14,2)
```

or Django:

```text
DecimalField(max_digits=14, decimal_places=2)
```

Examples:

```text
220.00
12500.50
```

Do not use floating-point types for financial values.

---

## Quantity

Use:

```text
NUMERIC(14,3)
```

This supports:

```text
10 PCS

2 COIL

12.500 MTR
```

The selected UnitOfMeasure determines whether fractional quantity is allowed.

---

# 11. Document Number Format

Keep document numbering simple.

## Invoice

```text
INV-000001
INV-000002
INV-000003
```

## Stock Entry

```text
STK-000001
```

## Cash Receipt / Payment

```text
RCV-000001
```

Prefix should be configurable through business settings.

Document numbers must have a database uniqueness constraint.

Do not make users manually enter official document numbers.

---

# 12. Posted Invoice Editing Rule

A POSTED invoice cannot be edited directly.

Staff cannot change:

- Customer
- Product
- Variant
- Quantity
- Price
- Total

after posting.

This prevents silent manipulation of financial and stock history.

---

# 13. Invoice Void / Reversal Rule

## V1 Rule

Only **Owner** may void a posted invoice.

Admin and Staff cannot void posted invoices.

---

## Invoice Without Payment

When Owner voids an unpaid POSTED invoice:

```text
Invoice
POSTED → VOID
```

and the system:

```text
restores the exact InventoryLots
```

through:

```text
REVERSAL StockMovement
```

Original history remains intact.

---

## Invoice With Payment

If an invoice has active payment allocations:

```text
Invoice cannot be voided immediately.
```

The related payment must first be voided/reversed.

Then the invoice may be voided.

This avoids creating negative or unexplained customer balances.

---

# 14. Payment Void Rule

Only Owner may void a POSTED payment.

Voiding a payment:

1. Marks Payment as `VOID`.
2. Invalidates/removes its active allocations.
3. Recalculates customer/invoice outstanding balances.
4. Keeps the payment record in history.

The payment must never simply disappear from the database.

---

# 15. Sales Return Decision

## V1 Decision

**Sales Return is NOT included in V1.**

There will be no:

```text
Return Sale screen
Return Invoice
Partial Item Return
Exchange Workflow
```

in the BDT 25,000 implementation.

---

## Correction vs Return

If an invoice was entered incorrectly immediately:

```text
Owner voids invoice
→ Correct invoice is created
```

If a customer genuinely returns goods after a completed sale:

```text
Future feature
```

This avoids building a return-accounting workflow before it is actually required.

---

# 16. Duplicate Submission Rule

Invoice, Stock Entry and Payment forms should use a unique submission token/idempotency key.

Example:

```text
submission_key = unique UUID
```

If the user:

- Double-clicks
- Refreshes
- Retries because the connection is slow

the same request must not create duplicate business transactions.

---

# 17. Exact Permission Matrix

V1 uses three fixed Django Groups:

```text
Owner
Admin
Staff
```

No configurable enterprise RBAC screen is required.

| Action | Owner | Admin | Staff |
|---|:---:|:---:|:---:|
| Login | ✓ | ✓ | ✓ |
| View Current Stock | ✓ | ✓ | ✓ |
| View Stock History | ✓ | ✓ | ✓ |
| Stock In | ✓ | ✓ | ✗ |
| Manage Products | ✓ | ✓ | ✗ |
| Manage Categories/Brands/Units | ✓ | ✓ | ✗ |
| Manage Customers | ✓ | ✓ | ✓ |
| Create Invoice | ✓ | ✓ | ✓ |
| Negotiate Above Minimum Price | ✓ | ✓ | ✓ |
| Sell Below Minimum Price | ✓ | ✗ | ✗ |
| Print Invoice | ✓ | ✓ | ✓ |
| Receive Payment | ✓ | ✓ | ✓ |
| View Customer Statement | ✓ | ✓ | ✓ |
| View Sales Report | ✓ | ✓ | ✓ |
| View Collection Report | ✓ | ✓ | ✓ |
| View Due Report | ✓ | ✓ | ✓ |
| View Gross Profit | ✓ | ✗ | ✗ |
| View Sensitive Cost Reports | ✓ | ✗ | ✗ |
| Void Payment | ✓ | ✗ | ✗ |
| Void Posted Invoice | ✓ | ✗ | ✗ |
| Manage Staff/Admin Users | ✓ | ✗ | ✗ |
| Change Business Settings | ✓ | ✗ | ✗ |

This is the V1 permission baseline.

Future clients may request a different matrix as separately scoped configuration.

---

# 18. Sales Price Permission Rule

Example:

```text
Cost:
৳180

Default:
৳220

Minimum:
৳190
```

Staff/Admin may sell at:

```text
৳190+
```

Owner may explicitly override below:

```text
৳190
```

When Owner sells below minimum, the system records:

```text
price_override = true
approved_by = Owner
```

No approval-request workflow is needed.

---

# 19. Canonical V1 Database Model

The business tables are:

```text
BusinessSetting

Category
Brand
UnitOfMeasure

Product
ProductVariant

StockEntry
InventoryLot
StockMovement

Customer

Invoice
InvoiceItem
InvoiceItemLotAllocation

Payment
PaymentAllocation
```

Authentication uses Django:

```text
User
Group
Permission
```

---

# 20. Core Relationship View

```text
Product
   ↓
ProductVariant
   ↓
InventoryLot
   ↓
StockMovement
```

Sales:

```text
Customer
   ↓
Invoice
   ↓
InvoiceItem
   ↓
InvoiceItemLotAllocation
   ↓
InventoryLot
```

Money:

```text
Customer
   ↓
Payment
   ↓
PaymentAllocation
   ↓
Invoice
```

---

# 21. User-Facing Concepts

Users should see:

```text
Product
Variant
Stock
Batch
Invoice
Payment
Due
Statement
Report
```

Users should NOT need to understand:

```text
InventoryLot
InvoiceItemLotAllocation
PaymentAllocation
Row Lock
FIFO Cost Layer
```

Those remain backend implementation details.

---

# 22. Canonical Business Calculations

## Current Stock

```text
SUM(active InventoryLot.quantity_remaining)
```

per variant.

---

## Invoice Due

```text
Invoice Total
-
Active Payment Allocations
```

---

## Customer Due

```text
Total POSTED Invoice Value
-
Total Active Payment Allocations
```

---

## Gross Profit

```text
Actual Invoice Revenue
-
Actual Inventory Lot Cost
```

---

# 23. First Engineering Deliverable

Do **not** begin with dashboard polish or all reports.

The first production-quality vertical slice should be:

```text
Product Setup
      ↓
Variant Setup
      ↓
Stock In
      ↓
Current Stock
      ↓
New Invoice
      ↓
Negotiated Price
      ↓
Partial Payment
      ↓
Stock Deduction
      ↓
Printable Invoice
      ↓
Customer Statement
```

This validates almost every critical business rule.

---

# 24. Foundation Sprint Order

## Sprint 1 — Foundation

Build:

```text
Django project
PostgreSQL
Environment settings
Custom User model
Django Groups
Permissions
Authentication
Business Settings
```

Infrastructure should remain simple.

---

## Sprint 2 — Master Data

Build:

```text
Category
Brand
UnitOfMeasure
Product
ProductVariant
Customer
```

Include simple management UI.

---

## Sprint 3 — Inventory

Build:

```text
StockEntry
InventoryLot
StockMovement
```

Primary service:

```text
add_stock()
```

Tests must cover:

- Positive quantity
- Invalid quantity
- Unit validation
- Lot creation
- Stock movement creation
- Correct current stock

---

# 25. Critical Sales Service

Implement:

```text
create_invoice()
```

inside a database transaction.

Conceptually:

```text
BEGIN

Validate customer
Validate invoice lines
Validate quantity
Validate negotiated price

Lock required InventoryLot rows

Re-check available stock

Consume FIFO lots

Create Invoice

Create InvoiceItems

Create InvoiceItemLotAllocations

Update quantity_remaining

Create SALE StockMovements

Create initial Payment if applicable

Create PaymentAllocation

COMMIT
```

If anything fails:

```text
ROLLBACK
```

---

# 26. Critical Invoice Tests

Before building polished UI, test:

### Normal Sale

```text
Stock = 10
Sell = 5
Remaining = 5
```

### Insufficient Stock

```text
Stock = 5
Sell = 8

→ Reject
```

### Negative Quantity

```text
→ Reject
```

### Negotiated Price

```text
Default = 220
Minimum = 190
Staff Price = 205

→ Accept
```

### Unauthorized Price

```text
Staff Price = 170

→ Reject
```

### Owner Override

```text
Owner Price = 170

→ Accept + record override
```

### Concurrent Sale

```text
Available = 10

User A requests 8
User B requests 7

→ only valid available quantity may be sold
```

### Duplicate Submission

Same submission key twice:

```text
→ only one Invoice
```

---

# 27. Payment Service

Implement:

```text
receive_payment()
```

Rules:

- Payment > 0
- Payment cannot exceed customer's total outstanding balance in V1
- Generic payment automatically allocates oldest invoices first
- Initial invoice payment allocates to that invoice
- All operations happen atomically

Tests should cover:

```text
Full payment
Partial payment
Payment across multiple invoices
Invalid negative payment
Overpayment
Duplicate submission
```

---

# 28. Void Services

After core flows are working, implement:

```text
void_payment()
void_invoice()
```

Only Owner can execute them.

Tests should prove:

- Payment void restores invoice due.
- Invoice void restores original stock lots.
- Stock reversal is traceable.
- Paid invoice cannot be voided before payment is voided.
- VOID transactions remain visible historically.

---

# 29. UI Implementation Order

Only after business services are tested, build the main staff UI in this order:

### 1. Stock In

```text
Product
→ Variant
→ Batch if required
→ Quantity
→ Cost
→ Save
```

### 2. New Sale

```text
Customer
→ Product
→ Variant
→ Qty
→ Negotiated Price
→ Payment
→ Complete Sale
```

### 3. Due Collection

```text
Customer
→ Outstanding
→ Payment
→ Receive
```

### 4. Current Stock

```text
Product
Variant
Available Quantity
```

### 5. Customer Statement

### 6. Invoice Print

Then:

```text
Dashboard
Reports
Administrative screens
```

---

# 30. Vertical Slice Acceptance Scenario

A complete first vertical slice should prove the following scenario.

## Step 1

Create:

```text
Product:
LED Bulb
```

Variant:

```text
12W / B22
PCS

Default Cost:
৳180

Default Sale:
৳220

Minimum Sale:
৳190
```

---

## Step 2

Stock In:

```text
50 pcs @ ৳180
```

Result:

```text
Current Stock:
50
```

---

## Step 3

Create customer:

```text
Rahman Electric
```

---

## Step 4

Create sale:

```text
10 pcs

Negotiated Price:
৳205
```

Result:

```text
Sale:
৳2,050
```

Stock:

```text
40 pcs
```

---

## Step 5

Customer pays:

```text
৳1,500
```

Result:

```text
Due:
৳550
```

---

## Step 6

Print invoice:

```text
Total: ৳2,050
Paid:  ৳1,500
Due:     ৳550
```

---

## Step 7

Customer statement:

```text
Invoice          +৳2,050
Payment          -৳1,500
-------------------------
Balance             ৳550
```

---

## Step 8

Gross profit:

```text
Revenue:
10 × 205 = ৳2,050

COGS:
10 × 180 = ৳1,800

Gross Profit:
৳250
```

If this complete flow passes reliably, the system foundation is correct.

---

# 31. Explicitly Deferred From V1

The following must not enter the first implementation unless separately re-scoped:

```text
Sales Returns
Purchase Management
Supplier Accounting
Raw Materials
BOM
Manufacturing
Expense Accounting
General Ledger
Balance Sheet
VAT/Tax
Multiple Warehouse
Barcode Hardware
Mobile App
SaaS Multi-tenancy
Subscription Billing
Complex Approval Workflow
Enterprise Audit System
```

---

# 32. Future Client Reuse

V1 remains:

```text
One Client
→ One Deployment
→ One Database
```

For another similar shop:

```text
Clone same codebase
→ New environment
→ New database
→ New BusinessSetting
→ New products/customers/users
→ Deploy
```

No tenant architecture is required now.

The core application must therefore avoid hardcoding:

```text
shop name
electrical-only categories
specific brands
specific product types
client-specific users
```

---

# 33. Locked V1 Decisions Summary

The following decisions are now locked:

```text
Inventory model
→ InventoryLot

External batch
→ Optional field on InventoryLot

Stock consumption
→ FIFO

Invoice lifecycle
→ DRAFT / POSTED / VOID

Payment state
→ Derived UNPAID / PARTIAL / PAID

Payment relationship
→ Always PaymentAllocation

Payment allocation
→ Oldest outstanding invoice first

Sales return
→ Not V1

Currency
→ BDT

Money precision
→ Decimal(14,2)

Quantity precision
→ Decimal(14,3)

Invoice number
→ INV-000001

Stock document
→ STK-000001

Payment receipt
→ RCV-000001

Posted invoice editing
→ Not allowed

Invoice void
→ Owner only + stock reversal

Payment void
→ Owner only

Gross profit
→ Actual sale price - actual lot cost

Permissions
→ Fixed Owner/Admin/Staff matrix

Multi-tenancy
→ Not V1

Future reuse
→ Clone + separate DB + separate deployment
```

These decisions should be treated as the implementation baseline unless an actual client requirement requires a deliberate change.
