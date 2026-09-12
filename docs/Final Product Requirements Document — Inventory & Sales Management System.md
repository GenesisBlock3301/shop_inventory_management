# Product Requirements Document (PRD)

## 1. Project Overview

**Project Name:** Inventory & Sales Management System  
**Initial Business Type:** Electrical Shop  
**Product Type:** Web-based Inventory, Sales, Collection & Due Management System  
**Initial Deployment:** One business / one shop  
**Target Users:** Owner/Admin + approximately 3–4 Staff  
**V1 Budget:** **BDT 25,000**  
**Estimated Delivery:** **3–4 Weeks**

The system will help the business manage:

- Finished-product stock entry
- Product variants / pack sizes
- Batch/Lot numbers where required
- Different purchase/production costs
- Standard and negotiated selling prices
- Current inventory
- Sales invoices
- Invoice printing
- Cash collection
- Partial payments
- Customer dues
- Customer statements
- Daily/monthly/yearly sales
- Monthly/yearly collection breakdown
- Current stock
- Basic gross profit
- Outstanding receivables
- Owner business-performance dashboard
- Staff permissions and transaction accountability

Primary workflow:

```text
Finished Product
→ Stock In
→ Inventory
→ Sales Invoice
→ Payment
→ Due
→ Customer Statement
→ Reports
```

---

# 2. Scope Clarification

## 2.1 Production Scope

V1 supports:

```text
Finished Product
→ Stock Posting
→ Inventory
```

V1 does not manage:

- Raw materials
- BOM
- Production orders
- Manufacturing planning
- Raw-material consumption
- Factory production costing

---

## 2.2 Accounts Scope

For V1, "Accounts" means operational business accounting:

- Sales
- Collections
- Customer dues
- Customer statements
- Stock movement
- Current inventory
- Basic gross-profit reporting
- Monthly and yearly business summaries

V1 does not include:

- General Ledger
- Chart of Accounts
- Journal Entries
- Trial Balance
- Balance Sheet
- Full Profit & Loss
- Cash Flow Statement
- Supplier Accounting
- Statutory Accounting

---

# 3. Product Goals

The system should:

- Keep inventory accurate.
- Support product variants and pack sizes.
- Support different rates.
- Support batch-wise stock where required.
- Allow quick invoice creation.
- Automatically reduce stock after sales.
- Print customer invoices.
- Record cash received.
- Support partial payments.
- Track customer dues.
- Produce customer statements.
- Show previous and current stock.
- Show daily, monthly and yearly sales.
- Show monthly and yearly collections.
- Show outstanding dues.
- Calculate basic gross profit.
- Give the Owner a month-by-month yearly business overview.
- Prevent unauthorized manipulation.
- Remain simple enough for shop staff.

---

# 4. Product Design Principles

## 4.1 Simple for Staff

Primary staff actions:

```text
Stock In
New Invoice
Receive Payment
Check Stock
Find Customer
Print Invoice
```

---

## 4.2 Useful for Owner

The Owner should quickly understand:

```text
How much was sold?
How much cash was collected?
How much is still due?
How much stock remains?
What was the gross profit?
How is this month performing?
How is this year performing?
Which months performed better or worse?
```

---

## 4.3 Backend Is Authoritative

The browser must never be trusted for:

- Selling price
- Quantity
- Available stock
- Invoice total
- Payment amount
- Due amount
- User permissions

---

## 4.4 Avoid Overengineering

V1 will not introduce unnecessary:

- Microservices
- Enterprise workflows
- Complex approval systems
- SaaS multi-tenancy
- Advanced accounting
- Enterprise fraud systems

---

# 5. User Roles

## Owner

Owner can:

- View all stock
- View sales
- View collections
- View dues
- View gross profit
- View monthly/yearly business performance
- Manage users
- Manage products
- Add stock
- Create invoices
- Receive payments
- View statements
- Access reports
- Override permitted price restrictions
- Void transactions where permitted

## Admin

Admin handles most operational activities according to permissions.

## Staff

Staff normally handles:

- Invoice creation
- Payments
- Customers
- Stock lookup
- Stock entry if permitted
- Invoice printing

---

# 6. Business Configuration

Configurable fields:

- Business Name
- Address
- Mobile Number
- Email
- Logo
- Invoice Prefix
- Invoice Header
- Invoice Footer
- Currency

---

# 7. Product Management

Model:

```text
Product
→ ProductVariant
```

Example:

```text
LED Bulb
→ 12W / B22 / 6500K
→ 15W / B22 / 6500K
```

Variants are actual sellable SKUs.

---

# 8. Category & Brand Management

Examples:

```text
Lighting
Cable
Fan
Switch & Socket
Circuit Breaker
Accessories
```

Brands remain configurable.

---

# 9. Unit of Measure

Examples:

```text
PCS
MTR
COIL
BOX
SET
ROLL
```

Fractional quantity is allowed only for suitable units.

---

# 10. Product Variant / Pack Handling

A variant may have:

- SKU
- Unit
- Standard price
- Minimum price
- Batch tracking setting
- Variant-specific stock

---

# 11. Inventory Model

Canonical inventory model:

```text
Product
→ ProductVariant
→ InventoryLot
→ StockMovement
```

`ProductBatch` will not exist as a separate V1 model.

`InventoryLot` represents each stock receipt/cost layer.

It may contain:

```text
variant
batch_no (optional)
quantity_received
quantity_remaining
unit_cost
received_at
```

---

# 12. Stock Entry

Authorized users can post finished products into inventory.

Fields:

- Product
- Variant
- Quantity
- Unit Cost
- Standard Selling Price
- Optional Batch Number
- Date

Example result:

```text
Previous Stock: 100
Added:            50
Current Stock:   150
```

---

# 13. Batch / Lot Handling

Batch number is optional unless the product requires batch tracking.

Example:

```text
InventoryLot
Batch No: B-2026-010
Qty: 50
Cost: ৳180
```

or:

```text
Batch No: NULL
```

Both are valid.

---

# 14. FIFO Stock Consumption

V1 uses:

**FIFO — First In, First Out**

Example:

```text
Lot A: 20 pcs @ ৳180
Lot B: 30 pcs @ ৳195
```

Sale of 25:

```text
20 from Lot A
5 from Lot B
```

This determines historical COGS and profit.

---

# 15. Current Inventory

Users can view:

| Product | Variant | Available | Unit |
|---|---|---:|---|
| LED Bulb | 12W/B22 | 140 | PCS |
| BBS Cable | 1.5mm²/90m | 18 | COIL |
| Schneider MCB | 32A/2P | 27 | PCS |

---

# 16. Stock Movement

`StockMovement` records why inventory changed.

Example:

```text
STOCK_IN    +50
SALE        -10
REVERSAL    +10
```

Current stock is not manually edited.

---

# 17. Negative Stock Prevention

If:

```text
Available = 5
Requested = 8
```

result:

```text
Sale rejected.
Only 5 units are available.
```

Backend validation is mandatory.

---

# 18. Customer / Party Management

Fields:

- Name
- Mobile
- Address
- Status

A default customer may exist:

```text
Walk-in Customer
```

Customer due is calculated from invoices and payments.

---

# 19. Invoice Lifecycle

Canonical lifecycle:

```text
DRAFT
POSTED
VOID
```

## DRAFT

Editable and does not affect stock.

## POSTED

Final sale; stock deducted.

## VOID

Cancelled through controlled reversal.

---

# 20. Derived Payment Status

Payment status is separate from invoice lifecycle.

```text
UNPAID
PARTIAL
PAID
```

Example:

```text
Invoice Total = ৳10,000
Payment = ৳6,000

Lifecycle = POSTED
Payment Status = PARTIAL
```

Payment status is derived rather than manually maintained.

---

# 21. New Sales Invoice

Typical flow:

```text
Customer
→ Product
→ Variant
→ Quantity
→ Negotiated Price
→ Payment
→ Complete Sale
```

Multiple items are supported.

---

# 22. Negotiated Selling Price

The system distinguishes:

```text
Standard Price
```

from:

```text
Actual Selling Price
```

Example:

```text
Standard:   ৳220
Negotiated: ৳205
```

`InvoiceItem.unit_price = ৳205`

---

# 23. Minimum Price Protection

Example:

```text
Cost:          ৳180
Default:       ৳220
Minimum:       ৳190
```

Staff/Admin can sell at or above ৳190.

Owner may explicitly override below minimum.

---

# 24. Client-Side Manipulation Protection

Backend validates independently:

- Price
- Quantity
- Stock
- Invoice totals
- Payments
- Permissions

---

# 25. Invoice Calculation

```text
Line Total
=
Quantity × Actual Selling Price
```

Invoice total is recalculated on the backend.

---

# 26. Invoice Posting

Posting must be atomic:

```text
Validate
→ Lock Inventory
→ Create Invoice
→ Create Items
→ Consume InventoryLots
→ Record StockMovement
→ Create Payment
→ Allocate Payment
→ Commit
```

Any failure:

```text
ROLLBACK
```

---

# 27. Concurrent Sales Protection

Database transactions and row locking must prevent overselling.

---

# 28. Duplicate Submission Protection

Important transaction forms should use an idempotency/submission key.

Duplicate clicks must not create duplicate records.

---

# 29. Invoice Printing

Printed invoice shows:

- Business details
- Invoice number
- Date
- Customer
- Product
- Variant
- Quantity
- Actual selling price
- Total
- Paid
- Due

Cost/minimum price/profit remain internal.

---

# 30. Payment Model

Canonical model:

```text
Customer
→ Payment
→ PaymentAllocation
→ Invoice
```

`Payment.invoice_id` is not used.

---

# 31. Payment Allocation

Payment during a new sale is allocated to that invoice.

General due collection automatically pays the oldest outstanding invoice first.

Example:

```text
INV-001 Due = ৳3,000
INV-002 Due = ৳5,000
Payment     = ৳4,000
```

Allocation:

```text
INV-001 = ৳3,000
INV-002 = ৳1,000
```

---

# 32. Partial Payment & Due

Example:

```text
Invoice: ৳15,300
Paid:    ৳10,000
Due:      ৳5,300
```

Later payment reduces the outstanding amount.

---

# 33. Customer Statement

Example:

| Date | Transaction | Bill | Payment | Balance |
|---|---|---:|---:|---:|
| Sep 12 | INV-001 | 15,300 | — | 15,300 |
| Sep 12 | Payment | — | 10,000 | 5,300 |
| Sep 15 | Payment | — | 3,000 | 2,300 |

---

# 34. Gross Profit

Formula:

```text
Actual Sales Revenue
-
Actual Historical Inventory Cost
```

Example:

```text
Sale Price = ৳205
Cost       = ৳180
Profit     = ৳25
```

---

# 35. Staff Dashboard

Staff dashboard prioritizes operations:

```text
[ New Invoice ]
[ Stock In ]
[ Receive Payment ]
[ Find Customer ]
```

Optional small indicators:

```text
Today's Sales
Today's Collection
Invoice Count
```

No complex business analytics are required for Staff.

---

# 36. Owner Dashboard

The Owner Dashboard is a key V1 business-summary screen.

It must support three primary views:

```text
Today
Monthly
Yearly
```

The Owner should be able to change the selected period without navigating into separate report modules.

---

## 36.1 Today's Summary

Show:

```text
Today's Sales
Today's Collection
Today's Outstanding Created
Today's Gross Profit
Invoice Count
```

---

## 36.2 Monthly Summary

The Owner can select a month and year.

Example:

```text
September 2026
```

Summary cards:

```text
Total Sales        ৳485,000
Total Collection   ৳430,000
Outstanding Due     ৳55,000
COGS               ৳392,000
Gross Profit         ৳93,000
Invoice Count             142
```

Optional supporting information:

```text
Top Selling Products
Customers With Highest Due
Low Stock Products
```

The dashboard should make it clear that:

```text
Sales ≠ Collection
```

because invoices can remain partially unpaid.

---

## 36.3 Yearly Summary

The Owner can select a year.

Example:

```text
Year: 2026
```

Summary:

```text
Total Sales         ৳5,850,000
Total Collection    ৳5,300,000
Current Outstanding   ৳550,000
COGS                ৳4,620,000
Gross Profit        ৳1,230,000
```

The yearly dashboard must also include a **month-by-month breakdown**.

Example:

| Month | Sales | Collection | Gross Profit |
|---|---:|---:|---:|
| January | ৳420,000 | ৳390,000 | ৳85,000 |
| February | ৳460,000 | ৳430,000 | ৳92,000 |
| March | ৳510,000 | ৳470,000 | ৳101,000 |
| ... | ... | ... | ... |
| December | ৳530,000 | ৳495,000 | ৳109,000 |

This allows the Owner to identify:

- Strong months
- Weak months
- Sales trends
- Collection trends
- Profit trends

A simple bar/line visualization may be added if it remains within V1 implementation effort.

The tabular breakdown is the required baseline.

---

## 36.4 Outstanding Due Rule

**Current outstanding due must not be calculated by adding each month's historical due.**

Example:

```text
January Invoice Due = ৳10,000
Customer pays it in February.
```

The January due should not remain part of the current yearly outstanding balance.

Therefore:

```text
Current Outstanding
=
Current unpaid portion of all POSTED invoices
```

Monthly/yearly sales and collection are period-based.

Outstanding receivable is a current balance.

---

# 37. Reports

V1 includes:

- Daily Sales
- Monthly Sales
- Yearly Sales
- Cash Collection
- Monthly Collection
- Yearly Collection
- Customer Due
- Customer Statement
- Current Stock
- Stock Movement
- Basic Gross Profit
- Monthly Gross Profit
- Yearly Gross Profit

Common filters:

```text
Date From
Date To
Month
Year
Customer
Product
Variant
```

---

# 38. Monthly Business Report

The Monthly Report answers:

```text
How much was sold?
How much was collected?
How much gross profit was generated?
How many invoices were created?
What is currently outstanding?
```

Example:

```text
September 2026

Sales              ৳485,000
Collection         ৳430,000
COGS               ৳392,000
Gross Profit        ৳93,000
Invoice Count             142
Current Outstanding ৳55,000
```

---

# 39. Yearly Business Report

The Yearly Report answers:

```text
How much was sold during the year?
How much was collected during the year?
What was total COGS?
What was gross profit?
What is currently outstanding?
How did individual months perform?
```

Example:

```text
2026

Sales               ৳5,850,000
Collection          ৳5,300,000
COGS                ৳4,620,000
Gross Profit        ৳1,230,000
Current Outstanding   ৳550,000
```

Followed by:

```text
January
February
March
...
December
```

breakdown.

---

# 40. Currency & Precision

V1 currency:

```text
BDT
```

Money:

```text
Decimal(14,2)
```

Quantity:

```text
Decimal(14,3)
```

Floating-point types must not be used for financial values.

---

# 41. Document Numbering

Invoice:

```text
INV-000001
```

Stock Entry:

```text
STK-000001
```

Payment Receipt:

```text
RCV-000001
```

Prefixes are configurable.

---

# 42. Void / Reversal Rules

Only Owner can void posted invoices/payments.

Posted invoice is not directly editable.

Invoice void restores the exact consumed InventoryLots through reversal records.

Paid invoices require related payment reversal before invoice voiding.

---

# 43. Sales Returns

**Sales Returns are explicitly outside V1.**

Incorrect recent invoices are handled through:

```text
Void
→ Recreate correctly
```

Actual customer-return workflows are future scope.

---

# 44. Permission Matrix

| Action | Owner | Admin | Staff |
|---|:---:|:---:|:---:|
| View Current Stock | ✓ | ✓ | ✓ |
| View Stock History | ✓ | ✓ | ✓ |
| Stock In | ✓ | ✓ | ✗ |
| Manage Products | ✓ | ✓ | ✗ |
| Manage Master Data | ✓ | ✓ | ✗ |
| Manage Customers | ✓ | ✓ | ✓ |
| Create Invoice | ✓ | ✓ | ✓ |
| Sell ≥ Minimum Price | ✓ | ✓ | ✓ |
| Sell Below Minimum | ✓ | ✗ | ✗ |
| Print Invoice | ✓ | ✓ | ✓ |
| Receive Payment | ✓ | ✓ | ✓ |
| Customer Statement | ✓ | ✓ | ✓ |
| Sales Reports | ✓ | ✓ | ✓ |
| Collection Reports | ✓ | ✓ | ✓ |
| Due Reports | ✓ | ✓ | ✓ |
| Owner Dashboard | ✓ | ✗ | ✗ |
| Monthly/Yearly Owner Analytics | ✓ | ✗ | ✗ |
| Gross Profit | ✓ | ✗ | ✗ |
| Sensitive Cost Reports | ✓ | ✗ | ✗ |
| Void Payment | ✓ | ✗ | ✗ |
| Void Posted Invoice | ✓ | ✗ | ✗ |
| Manage Users | ✓ | ✗ | ✗ |
| Business Settings | ✓ | ✗ | ✗ |

---

# 45. Transaction Accountability

Important records should preserve:

```text
created_by
created_at
voided_by
voided_at
```

where applicable.

---

# 46. Technical Direction

Stack:

```text
Python
Django
PostgreSQL
Django Templates
HTMX
Alpine.js
Bulma
```

Architecture:

```text
Modular Monolith
```

---

# 47. Reusable Product Design

Business-specific configuration should remain data-driven:

- Business identity
- Categories
- Brands
- Units
- Products
- Variants
- Prices
- Users
- Invoice prefixes

Avoid hardcoded electrical-specific application logic.

---

# 48. Near-Future Reuse

Future client model:

```text
Client A
→ Deployment A
→ Database A

Client B
→ Deployment B
→ Database B
```

Same core codebase.

No multi-tenancy is required.

---

# 49. V1 Out of Scope

- Raw Material Management
- BOM
- Manufacturing workflow
- Full Accounting
- General Ledger
- Balance Sheet
- Full P&L
- Supplier Accounting
- Purchase Management
- VAT/Tax
- Multiple Warehouses
- Sales Returns
- Payroll
- Mobile App
- Complex Approval Workflow
- Enterprise Fraud Detection
- Barcode Hardware Integration
- E-commerce Integration
- Payment Gateway
- SMS Integration
- SaaS
- Multi-Tenancy
- Subscription Billing

---

# 50. Acceptance Criteria

V1 is complete when:

1. Finished goods can be posted into stock.
2. Products support multiple variants.
3. Different variants support different rates.
4. Batch numbers can be recorded where required.
5. Current stock can be viewed.
6. FIFO lot consumption works.
7. Stock movement history exists.
8. Negative stock is prevented.
9. Customers can be managed.
10. Multi-item invoices work.
11. Negotiated selling prices work.
12. Minimum-price restrictions work.
13. Backend recalculates totals.
14. Invoice and stock operations are atomic.
15. Concurrent sales cannot oversell stock.
16. Invoice printing works.
17. Full/partial payments work.
18. PaymentAllocation is used consistently.
19. Customer due is accurate.
20. Customer statements work.
21. Daily sales reporting works.
22. Monthly sales reporting works.
23. Yearly sales reporting works.
24. Monthly collection reporting works.
25. Yearly collection reporting works.
26. Gross-profit calculation uses historical InventoryLot cost.
27. Monthly gross-profit summary works.
28. Yearly gross-profit summary works.
29. **Owner Dashboard supports Today / Monthly / Yearly views.**
30. **Owner can select a month and see Sales, Collection, COGS, Gross Profit, Invoice Count and Current Outstanding.**
31. **Owner can select a year and see annual totals.**
32. **Yearly view provides January–December month-by-month Sales, Collection and Gross Profit breakdown.**
33. Current outstanding receivable is calculated from unpaid invoice balances rather than summed historical monthly dues.
34. Permissions are enforced server-side.
35. Staff cannot modify protected posted transactions.
36. Important transactions identify the responsible user.
37. Business information is configurable.
38. The codebase can be cloned for another similar shop without redesigning the core system.

---

# 51. Final V1 Product Strategy

The system should provide:

```text
Finished Goods
→ Stock
→ Sales
→ Negotiated Price
→ Collection
→ Due
→ Customer Statement
→ Monthly Performance
→ Yearly Performance
→ Gross Profit
```

For Staff:

```text
Fast operational workflow
```

For Owner:

```text
Today
→ What happened today?

Monthly
→ How did this month perform?

Yearly
→ How did the whole year perform,
   and which months were strongest or weakest?
```

The guiding rule remains:

> **Build what the first real shop needs, keep staff workflows simple, give the Owner clear business visibility, protect business-critical calculations on the backend, and keep the core reusable for the next similar client.**