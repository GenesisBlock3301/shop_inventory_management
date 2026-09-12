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
- Daily/monthly sales
- Current stock
- Basic gross profit
- Outstanding receivables
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

The product should remain simple for normal shop users while protecting business-critical data from unauthorized manipulation.

---

# 2. Scope Clarification

## 2.1 Production Scope

The client requires produced/finished goods to be added into stock.

V1 therefore supports:

```text
Finished Product
→ Stock Posting
→ Inventory
```

V1 does **not** manage:

- Raw materials
- BOM
- Production orders
- Manufacturing planning
- Raw-material consumption
- Factory production costing

Manufacturing workflow is outside the BDT 25,000 scope.

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

V1 does **not** include:

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
- Support different product variants and pack sizes.
- Support different rates.
- Support batch-wise stock where required.
- Allow quick invoice creation.
- Automatically reduce stock after sales.
- Print customer invoices.
- Record cash received.
- Support partial payments.
- Track customer dues.
- Produce customer statements.
- Show how much stock existed and how much remains.
- Show daily/monthly sales.
- Show collections.
- Show outstanding dues.
- Calculate basic gross profit.
- Prevent unauthorized stock, price, invoice and payment manipulation.
- Remain simple enough for shop staff to use with minimal training.

---

# 4. Product Design Principles

## 4.1 Simple for Staff

Primary staff actions should be limited to:

```text
Stock In
New Invoice
Receive Payment
Check Stock
Find Customer
Print Invoice
```

Technical database concepts must remain hidden.

---

## 4.2 Useful for Owner

The owner should quickly understand:

```text
How much was sold?
How much cash was collected?
How much is still due?
How much stock remains?
What was the gross profit?
Who created the transaction?
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

Frontend validation improves usability.

Backend validation protects the business.

---

## 4.4 Avoid Overengineering

V1 should not introduce unnecessary:

- Microservices
- Enterprise workflows
- Complex approval systems
- SaaS multi-tenancy
- Advanced accounting
- Enterprise fraud systems

---

## 4.5 Future Reuse

The first implementation is for one electrical shop.

The same codebase should later be reusable for other shops with similar workflows through:

```text
Clone Application
→ Configure Business
→ Create New Database
→ Add New Products
→ Create Users
→ Deploy Separately
```

Future reuse must not make V1 harder to use.

---

# 5. User Roles

## 5.1 Owner

Owner can:

- View all stock.
- View sales.
- View collections.
- View dues.
- View gross profit.
- Manage users.
- Manage products.
- Add stock.
- Create invoices.
- Receive payments.
- View customer statements.
- Access all reports.
- Perform permitted price overrides.
- Void incorrect transactions where supported.

---

## 5.2 Admin

Admin can perform most operational activities according to assigned permissions.

---

## 5.3 Staff

Staff can normally:

- Create invoices.
- Receive payments.
- Search customers.
- View available stock.
- Add stock if permitted.
- Print invoices.

Staff should not automatically access:

- User management
- Sensitive configuration
- Gross-profit information
- Unauthorized price override
- Historical transaction modification

---

# 6. Business Configuration

Business information must be configurable.

Fields may include:

- Business Name
- Address
- Mobile Number
- Email
- Logo
- Invoice Prefix
- Invoice Header
- Invoice Footer
- Currency

Example:

```text
Business Name:
ABC Electrical
```

must come from configuration rather than hardcoded application logic.

---

# 7. Product Management

The system must separate:

```text
Product
→ Product Variant / SKU
```

Example:

```text
Product:
LED Bulb

Variants:
12W / B22 / 6500K
15W / B22 / 6500K
20W / B22 / 6500K
```

Another example:

```text
Product:
MCB

Variants:
16A / 1P
32A / 2P
63A / 4P
```

Another:

```text
Product:
Cable

Variants:
1.5mm² / Red
2.5mm² / Black
4mm² / Red
```

The product model must not assume all products use the same measurement type.

---

# 8. Category & Brand Management

Categories should be configurable.

Electrical-shop examples:

```text
Lighting
Cable
Fan
Switch & Socket
Circuit Breaker
Accessories
```

Brands may include:

```text
Super Star
BBS
Schneider
Walton
MK
```

Future shops may create different categories and brands without source-code changes.

---

# 9. Unit of Measure

Supported units may include:

```text
PCS
MTR
COIL
BOX
SET
ROLL
```

Examples:

```text
LED Bulb → PCS
Cable → MTR
Cable Pack → COIL
```

Fractional quantity should only be allowed where appropriate.

---

# 10. Pack Size / Variant Handling

The client's "different pack sizes" requirement will be handled through product variants.

Example:

```text
Product:
Cable

Variants:
1.5mm² / 10m
1.5mm² / 50m
1.5mm² / 90m
```

Each variant can have:

- Separate SKU
- Separate stock
- Separate cost
- Separate selling price
- Separate minimum selling price
- Separate unit
- Separate batch tracking rule

---

# 11. Stock Entry

Authorized users can post finished products into inventory.

Stock entry fields:

- Product
- Variant / Pack Size
- Quantity
- Unit Cost
- Standard Selling Price
- Optional Batch Number
- Date

Example:

```text
Product:
LED Bulb

Variant:
12W / B22

Quantity:
50

Unit Cost:
৳180

Standard Price:
৳220

Batch:
Optional
```

After saving:

```text
Previous Stock: 100
Added:           50
Current Stock:  150
```

---

# 12. Batch / Lot Handling

Batch/Lot Number must be supported because it is part of the original client requirement.

Batch tracking can be:

```text
Required
```

for products where the business needs batch-wise tracking.

or:

```text
Optional
```

for normal retail products where batch information is not practically used.

Internally, the system may maintain inventory lots even when an external batch number is absent.

---

# 13. Current Inventory

Users should be able to view current stock.

Example:

| Product | Variant | Available | Unit |
|---|---|---:|---|
| LED Bulb | 12W/B22 | 140 | PCS |
| BBS Cable | 1.5mm²/90m | 18 | COIL |
| Schneider MCB | 32A/2P | 27 | PCS |

Basic search should support product and variant lookup.

---

# 14. Stock Movement History

The system must preserve stock history.

Example:

```text
LED Bulb 12W

STOCK IN      +100
STOCK IN       +50
SALE           -10
------------------
CURRENT         140
```

The owner should be able to understand:

```text
How much stock existed?
How much was added?
How much was sold?
How much remains?
```

---

# 15. Negative Stock Prevention

The system must reject:

```text
Requested Quantity > Available Quantity
```

Example:

```text
Available:
5

Requested:
8
```

Result:

```text
Cannot complete sale.
Only 5 units are available.
```

This must be checked on the backend during final invoice posting.

---

# 16. Customer / Party Management

Customer information:

- Name
- Mobile
- Address
- Status

A standard customer may exist for normal cash sales:

```text
Walk-in Customer
```

Customer due must come from sales and payments rather than an arbitrary manually edited balance.

---

# 17. New Sales Invoice

Users should be able to create invoices directly from available stock.

Typical UI:

```text
Customer:
Rahman Electric

Product:
LED Bulb

Variant:
12W / B22

Available:
140 pcs

Standard Price:
৳220

Selling Price:
৳205

Quantity:
10

Total:
৳2,050
```

Multiple products must be supported in one invoice.

---

# 18. Negotiated Selling Price

Bangladesh retail/wholesale bargaining must be supported.

The system should distinguish:

```text
Standard / Suggested Price
```

from:

```text
Actual Selling Price
```

Example:

```text
Suggested Price:
৳220

Negotiated Price:
৳205
```

The negotiated value becomes the actual historical sale price.

Reports and profit calculations must use the actual selling price.

---

# 19. Minimum Selling Price Protection

Each variant may have:

- Default Selling Price
- Minimum Selling Price

Example:

```text
Cost:               ৳180
Default Price:      ৳220
Minimum Price:      ৳190
```

Staff may sell:

```text
৳190
৳200
৳205
৳220
```

but if Staff submits:

```text
৳170
```

the backend must reject the sale.

Owner/Admin may override if business policy allows.

V1 will use simple permission-based override, not a complex approval workflow.

---

# 20. Client-Side Manipulation Protection

A user may attempt to manipulate:

- HTML
- JavaScript
- HTMX request
- Selling price
- Quantity
- Invoice total
- Payment amount

The backend must independently validate everything.

Example:

```text
Minimum Allowed:
৳190

Browser submits:
৳120
```

Result:

```text
Rejected
```

---

# 21. Invoice Calculation

Backend calculates:

```text
Line Total
=
Quantity × Actual Selling Price
```

Invoice Total:

```text
Sum of all Invoice Items
```

The backend must ignore manipulated totals submitted by the browser.

---

# 22. Invoice Posting

Invoice posting and stock deduction must happen as one atomic operation.

Conceptual flow:

```text
Validate Customer
→ Validate Products
→ Validate Quantity
→ Validate Selling Price
→ Re-check Stock
→ Create Invoice
→ Create Invoice Items
→ Deduct Stock
→ Record Stock Movements
→ Record Initial Payment
→ Commit Transaction
```

If any critical operation fails:

```text
Rollback
```

The system must never create:

```text
Invoice without stock deduction
```

or:

```text
Stock deduction without invoice
```

---

# 23. Concurrent Sales Protection

Example:

```text
Available Stock = 10

Staff A tries to sell 8
Staff B tries to sell 7
```

Both transactions must not succeed.

Stock must be revalidated and locked during final sale posting.

---

# 24. Invoice Status

Invoices should support:

```text
DRAFT
POSTED
VOID
```

Draft:

```text
Editable
```

Posted:

```text
Official Business Transaction
```

Normal Staff users must not silently modify posted invoices.

Payment status is derived separately from the invoice lifecycle:

```text
UNPAID   = no payment has been allocated
PARTIAL  = some, but not all, of the invoice total has been allocated
PAID     = the full invoice total has been allocated
```

`UNPAID`, `PARTIAL`, and `PAID` must not replace the `DRAFT`, `POSTED`, and
`VOID` lifecycle states.

---

# 25. Duplicate Submission Protection

Repeated clicks, browser refreshes or slow requests should not easily create duplicate invoices.

Invoice numbers must be unique.

---

# 26. Invoice Printing

After successful sale:

```text
✓ Sale Completed

Invoice: INV-0001
Total:   ৳15,300
Paid:    ৳10,000
Due:      ৳5,300

[ Print Invoice ]
```

Printed invoice should show:

- Business information
- Invoice number
- Date
- Customer
- Products
- Variants
- Quantity
- Actual selling price
- Total
- Paid
- Due

Internal information such as:

- Cost price
- Minimum selling price
- Profit

must not appear on customer invoices.

---

# 27. Cash Collection

The client requires sold money to be recorded as cash received.

Supported payment methods:

```text
CASH
BANK
MOBILE BANKING
OTHER
```

Example:

```text
Invoice:
৳15,300

Cash Received:
৳10,000

Outstanding:
৳5,300
```

The system should maintain:

- Payment amount
- Payment date
- Customer
- Payment method
- User who received it

---

# 28. Partial Payment & Due

If the customer pays less than the invoice amount:

```text
Invoice:
৳15,300

Payment:
৳10,000

Due:
৳5,300
```

the remaining amount automatically becomes outstanding.

---

# 29. Later Due Payment

Example:

```text
Customer:
Rahman Electric

Outstanding:
৳5,300

Receive:
৳3,000
```

Result:

```text
Payment Received:
৳3,000

Remaining Due:
৳2,300
```

The previous payment must remain in payment history.

---

# 30. Payment Protection

Backend must reject:

- Negative payment
- Invalid zero payment
- Invalid over-allocation
- Payment manipulation
- Unauthorized payment changes

Payment history must not be replaced by simply editing a single due field.

---

# 31. Customer / Party Statement

Example:

| Date | Transaction | Bill | Payment | Balance |
|---|---|---:|---:|---:|
| Sep 12 | INV-001 | 15,300 | — | 15,300 |
| Sep 12 | Cash Receive | — | 10,000 | 5,300 |
| Sep 15 | Cash Receive | — | 3,000 | 2,300 |

Summary:

```text
Total Purchase
Total Payment
Outstanding Due
```

This directly covers the client's party-statement requirement.

---

# 32. Gross Profit

Basic gross profit must use:

```text
Actual Selling Revenue
-
Actual Historical Stock Cost
```

Example:

```text
Cost:
৳180

Suggested Price:
৳220

Negotiated Sale:
৳205
```

Actual profit:

```text
৳205 - ৳180
=
৳25 per unit
```

Profit must never be calculated using the suggested price when the product was sold at a lower negotiated rate.

---

# 33. Historical Cost Protection

Example:

```text
January Stock:
Cost = ৳180

March Stock:
Cost = ৳195
```

A January sale must continue using the January stock cost.

Future stock cost changes must not modify old profit calculations.

---

# 34. Staff Dashboard

Staff dashboard should focus on operational actions:

```text
[ New Invoice ]
[ Stock In ]
[ Receive Payment ]
[ Find Customer ]
```

Optional small summary:

```text
Today's Sales
Today's Collection
Invoice Count
```

Staff should not be overwhelmed with unnecessary analytics.

---

# 35. Owner Dashboard

Owner dashboard may show:

```text
Today's Sales
Monthly Sales
Today's Collection
Outstanding Due
Current Stock
Gross Profit
Low Stock
```

The owner should understand the business situation quickly.

---

# 36. Reports

V1 reports:

- Daily Sales
- Monthly Sales
- Cash Collection
- Monthly Collection
- Customer Due
- Customer Statement
- Current Stock
- Stock Movement
- Basic Gross Profit

Filters may include:

```text
Date From
Date To
Customer
Product
Variant
```

---

# 37. Monthly Business Summary

The system should answer the client's requested monthly questions:

```text
How much was sold this month?
How much cash was collected?
How much remains due?
How much stock remains?
How much gross profit was generated?
```

Example:

```text
Monthly Sales        ৳485,000
Collection           ৳430,000
Outstanding Due       ৳55,000
COGS                  ৳392,000
Gross Profit           ৳93,000
```

---

# 38. Authentication & Authorization

Use Django's authentication system.

Roles:

```text
Owner
Admin
Staff
```

Permissions must be enforced on the backend.

Hiding a button or menu is not sufficient.

If Staff manually attempts a restricted URL or request:

```text
→ Deny
```

---

# 39. Transaction Accountability

Important operational records should preserve:

```text
created_by
created_at
```

Where relevant:

```text
voided_by
voided_at
approved_by
```

The owner should be able to identify which staff member performed important transactions.

This is basic accountability, not a full enterprise audit platform.

---

# 40. Staff User Experience

Normal staff should primarily see:

```text
Dashboard

Sales
- New Invoice
- Invoice List

Inventory
- Current Stock
- Stock In

Customers
- Customer List
- Receive Payment

Reports
```

Owner/Admin may additionally see:

```text
Products
Users
Settings
Gross Profit
Sensitive Reports
```

Technical concepts such as:

```text
InventoryLot
InvoiceItemLotAllocation
PaymentAllocation
```

must not be exposed to normal shop users.

---

# 41. Important Edge Cases

## Insufficient Stock

```text
→ Reject Sale
```

## Zero or Negative Quantity

```text
→ Reject
```

## Unauthorized Low Selling Price

```text
→ Reject or require Owner/Admin permission
```

## Browser-Manipulated Selling Price

```text
→ Backend validates independently
```

## Manipulated Invoice Total

```text
→ Backend recalculates
```

## Duplicate Submission

```text
→ Prevent duplicate invoice where reasonably possible
```

## Concurrent Sale

```text
→ Stock must never become negative
```

## Negative Payment

```text
→ Reject
```

## Unauthorized URL Access

```text
→ Backend denies
```

## Editing Posted Invoice

```text
→ Staff cannot silently change it
```

## Inactive Product

Old invoice history must remain available even when a product is no longer sold.

---

# 42. Technical Direction

V1 technical stack:

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

Deployment:

```text
Low-cost Django-compatible shared hosting
```

or:

```text
Small VPS
```

depending on hosting cost and reliability.

---

# 43. Reusable Product Design

Although the first business is an electrical shop, the codebase should remain reasonably generic.

Avoid business rules such as:

```text
if product == "LED Bulb"
```

or:

```text
category = "Electrical"
```

Core configuration should come from data:

- Categories
- Brands
- Units
- Products
- Variants
- Prices
- Business information
- Users
- Permissions

---

# 44. Near-Future Client Reuse

Near-future commercialization will use separate deployments.

Example:

```text
Client A
Electrical Shop
→ Deployment A
→ Database A


Client B
Hardware Shop
→ Deployment B
→ Database B


Client C
Electronics Shop
→ Deployment C
→ Database C
```

Same core codebase.

Different:

- Business settings
- Products
- Categories
- Brands
- Prices
- Customers
- Users
- Database

---

# 45. Future Applicable Businesses

The same product may later be reused for businesses with substantially similar workflows, such as:

```text
Electrical Shop
Hardware Shop
Electronics Shop
Motorcycle Parts Shop
Accessories Shop
Small Wholesale Shop
Building Materials Shop
```

provided their core workflow remains:

```text
Product
→ Variant
→ Stock
→ Sale
→ Negotiated Price
→ Payment
→ Due
→ Report
```

---

# 46. V1 Future-Reuse Requirements

V1 must make these configurable:

- Business identity
- Invoice prefix
- Categories
- Brands
- Units
- Products
- Variants
- Standard prices
- Minimum prices
- Users
- Roles

Another similar client should mainly require:

```text
Clone
+
Configure
+
Enter/Import Data
+
Deploy
```

rather than core source-code redesign.

---

# 47. Explicitly Not Included for Future Reuse

Do not build in V1:

- Multi-tenancy
- Tenant IDs
- SaaS subscription plans
- Centralized tenant billing
- Shared multi-business database
- Automated tenant onboarding
- Per-client feature-flag platform
- Complex plugin architecture
- Automated deployment platform

These may be considered only after multiple paying customers validate the product.

---

# 48. Out of Scope — BDT 25,000 V1

The following are outside V1:

- Raw Material Management
- BOM / Manufacturing
- Full Accounting
- General Ledger
- Balance Sheet
- Full P&L
- Supplier Accounting
- Purchase Management
- VAT/Tax Management
- Multiple Warehouses
- Advanced Returns
- Payroll
- Mobile Application
- Complex Approval Workflow
- Enterprise Fraud Detection
- Enterprise Audit Platform
- Barcode Hardware Integration
- E-commerce Integration
- Payment Gateway
- SMS Integration
- SaaS
- Multi-Tenancy
- Subscription Billing
- Automated Client Provisioning

---

# 49. Acceptance Criteria

V1 will be considered complete when:

1. Finished products can be posted into stock.
2. Products can have multiple variants / pack sizes.
3. Different variants can have different rates.
4. Batch/Lot numbers can be recorded where required.
5. Categories, brands and units are configurable.
6. Current stock can be viewed.
7. Stock history is maintained.
8. Negative stock is prevented.
9. Customers can be created and searched.
10. Multi-item sales invoices can be created.
11. Negotiated selling prices are supported.
12. Unauthorized low-price sales are blocked.
13. Backend recalculates invoice totals.
14. Invoice creation and stock deduction remain consistent.
15. Concurrent sales cannot create negative stock.
16. Invoices can be printed.
17. Cash collection can be recorded.
18. Full and partial payments work.
19. Later due payments work.
20. Payment history remains available.
21. Customer outstanding balances are accurate.
22. Customer statements are available.
23. Daily/monthly sales reports work.
24. Collection reports work.
25. Current stock reports work.
26. Outstanding due reports work.
27. Basic gross-profit reports use actual selling price and historical cost.
28. Staff cannot silently modify protected posted transactions.
29. Permissions are enforced server-side.
30. Important transactions identify the responsible user.
31. Business information is configurable.
32. The same codebase can later be cloned and deployed for another similar shop without redesigning the core system.

---

# 50. Final V1 Product Strategy

Build:

```text
A simple
reliable
low-cost
single-business
inventory + sales + collection + due system
```

that directly supports the original client requirement:

```text
Finished Goods
→ Stock Posting
→ Pack/Variant
→ Batch/Rate
→ Invoice
→ Print
→ Cash Receive
→ Partial Payment
→ Due Statement
→ Stock Report
→ Monthly Sales
→ Gross Profit
→ Outstanding Due
```

while keeping the application configurable enough that the same codebase can later serve another similar shop through a separate deployment.

The guiding rule is:

> **Build exactly what the first shop needs now, keep it simple for real users, protect critical business data on the backend, and avoid hardcoding decisions that would prevent the same product from being reused for the next similar client.**
