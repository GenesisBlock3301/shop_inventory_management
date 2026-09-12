# Product Requirements Document (PRD)

## 1. Project Overview

**Project Name:** Inventory & Sales Management System  
**Project Type:** Web-based Business Management Application  
**Target Users:** Business Owner/Admin and 3–4 Staff Users  
**Estimated Budget:** BDT 25,000–30,000  
**Estimated Delivery:** 3–4 Weeks

The system will manage:

- Products and variants
- Stock entry and stock movement
- Sales invoices
- Negotiated selling prices
- Customer payments
- Outstanding dues
- Customer statements
- Sales and stock reports
- Basic gross profit
- Staff access and business-control rules

The primary workflow is:

**Stock Entry → Invoice Creation → Sale → Cash Collection → Due Tracking → Stock & Sales Reporting**

A major objective is also to prevent unauthorized or unethical manipulation of prices, stock, invoices, and payments by operational users.

---

# 2. Product Goals

The system should:

- Maintain accurate inventory.
- Support different electrical product variants.
- Support different units such as PCS, MTR, COIL, BOX, and SET.
- Create and print sales invoices.
- Allow negotiated selling prices.
- Automatically deduct stock after sales.
- Record full and partial payments.
- Track outstanding balances.
- Provide customer statements.
- Provide daily/monthly sales reports.
- Provide stock reports.
- Calculate basic gross profit.
- Maintain user accountability.
- Prevent unauthorized price manipulation.
- Prevent negative stock.
- Prevent unauthorized modification of finalized transactions.
- Maintain enough transaction history to identify who performed important actions.

---

# 3. User Roles

## 3.1 Owner

Owner has full operational visibility.

Owner can:

- View stock.
- View cost price.
- View selling price.
- View gross profit.
- Create invoices.
- Receive payments.
- View all customer statements.
- Manage users.
- Override restricted selling prices where explicitly allowed.
- Void/reverse transactions where supported.

## 3.2 Admin

Admin can manage most operational activities based on assigned permissions.

Admin may:

- Manage products.
- Add stock.
- Create invoices.
- Receive payments.
- View reports.
- Manage customers.
- Approve restricted price overrides.

## 3.3 Staff

Staff performs normal shop operations.

Staff may:

- Add stock if permitted.
- Create invoices.
- Receive payments.
- View customers.
- View available stock.

Staff should not automatically receive access to:

- User management.
- Sensitive configuration.
- Unauthorized price override.
- Historical transaction editing.
- Cost/profit reports unless explicitly permitted.

---

# 4. Product Management

The system should support electrical-shop products such as:

- LED Bulbs
- Cables
- MCBs
- Switches
- Sockets
- Fans
- Circuit Breakers
- Electrical accessories

Product and actual sellable variant should be separated.

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

Variant characteristics must therefore not assume that every product uses wattage.

---

# 5. Unit of Measure

Each sellable variant should have a unit of measure.

Examples:

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

90m Cable Pack → COIL
```

The system should support fractional quantity only where the selected unit permits it.

---

# 6. Stock Management

## 6.1 Stock Entry

Authorized users can add stock.

A stock entry should include:

- Product
- Variant
- Quantity
- Unit Cost
- Optional Batch/Lot Number
- Date
- User

## 6.2 Batch Handling

Batch number should be optional.

Products that require batch tracking may enable it.

Products such as normal switches, bulbs, sockets, etc. should not be forced to enter a batch number unnecessarily.

## 6.3 Stock Deduction

When a sale is successfully posted:

```text
Available Stock
-
Sold Quantity
=
Remaining Stock
```

The deduction must happen automatically.

## 6.4 Negative Stock Prevention

The system must never allow:

```text
sale quantity > available quantity
```

Example:

```text
Available = 5

Attempted Sale = 8
```

Result:

```text
Sale Rejected
```

This validation must happen on the server.

---

# 7. Stock History

The system should maintain stock movement history.

Examples:

```text
STOCK_IN    +50

SALE        -10
```

The owner should be able to understand why stock changed.

Example:

```text
LED Bulb 12W

+100 Opening Stock
+50 Stock Added
-10 Invoice INV-001

Current = 140
```

---

# 8. Customer / Party Management

Customer data should contain:

- Name
- Mobile
- Address
- Status

A default:

```text
Walk-in Customer
```

may be used for normal retail cash sales.

Customer due should be determined from sales and payments, rather than manually maintained as an arbitrary value.

---

# 9. Sales Invoice

An invoice should include:

- Invoice Number
- Date
- Customer
- Products
- Variants
- Quantity
- Standard/List Price
- Actual Selling Price
- Line Total
- Invoice Total
- Payment
- Outstanding Amount

The system should support multiple products in one invoice.

---

# 10. Negotiated / Bargained Selling Price

The electrical-shop workflow must support bargaining.

Example:

```text
Default Selling Price:
৳220

Negotiated Price:
৳205
```

The system should therefore distinguish between:

```text
Default/List Price
```

and:

```text
Actual Selling Price
```

The actual negotiated selling price should be stored permanently with the invoice item.

Example:

```text
List Price = 220

Actual Selling Price = 205

Quantity = 10

Actual Revenue = 2,050
```

Reports and profit calculations must use the actual selling price.

---

# 11. Price Manipulation Protection

The selling-price field may be editable in the UI, but the browser must never be considered authoritative.

A staff member could technically manipulate:

- HTML
- JavaScript
- HTMX request
- Network request
- Form values

Therefore the backend must independently verify every selling price.

Example:

```text
Cost Price = 180

Allowed Minimum Price = 185

Staff submits:
150
```

The server must reject the transaction even if the browser UI was modified.

---

# 12. Minimum Selling Price

Each product variant should support an optional:

```text
Minimum Selling Price
```

Example:

```text
Cost Price:            ৳180

Default Sale Price:    ৳220

Minimum Sale Price:    ৳190
```

Staff may bargain between:

```text
৳190 – ৳220+
```

but cannot normally sell below:

```text
৳190
```

without authorization.

If no explicit minimum selling price is configured, the business may use cost price as the minimum permitted threshold.

---

# 13. Price Override

If the business wants to sell below the minimum price, the system should require an authorized user.

Example:

```text
Standard Price:    ৳220

Minimum Price:     ৳190

Requested Price:   ৳175
```

Staff result:

```text
Price below permitted limit.

Owner/Admin approval required.
```

Owner/Admin may override where business policy permits.

The system should record:

- Who requested it.
- Who approved it.
- Actual selling price.
- Date/time.

For the initial budget, approval may be implemented as a simple permission-controlled override rather than a complex approval workflow.

---

# 14. Invoice Calculation

The system calculates:

```text
Line Total
=
Quantity × Actual Selling Price
```

Example:

```text
Quantity = 10

Negotiated Price = 205

Line Total = 2,050
```

Invoice total:

```text
Invoice Total
=
Sum of all invoice lines
```

The server must recalculate all totals instead of trusting totals submitted by JavaScript.

---

# 15. Invoice Posting

Invoice creation and stock deduction must be one atomic transaction.

Conceptually:

```text
Validate Customer

Validate Items

Validate Stock

Validate Selling Prices

Create Invoice

Create Invoice Items

Deduct Stock

Create Stock History

Record Payment

Commit
```

If any important step fails:

```text
Rollback Entire Sale
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

# 16. Duplicate Submission Protection

A user may accidentally:

- Double-click Save.
- Refresh the page.
- Retry a slow request.

The system should prevent creation of duplicate invoices from the same submission where reasonably possible.

Invoice numbers must always be unique.

---

# 17. Concurrent Sale Protection

Two staff members may attempt to sell the same remaining stock simultaneously.

Example:

```text
Available Stock = 10

Rafiq wants 8

Karim wants 7
```

Both sales must not succeed.

The backend/database must re-check and lock stock during final invoice posting.

---

# 18. Invoice Status

Invoices should support:

```text
DRAFT
POSTED
VOID
```

A draft may still be changed.

A posted invoice becomes an official business transaction.

Posted invoices should not be freely editable by staff.

---

# 19. Historical Transaction Protection

Once an invoice is posted, staff should not be able to silently modify:

- Quantity
- Product
- Price
- Customer
- Total

because this would alter stock and financial history.

If a finalized transaction must be corrected, the application should use a controlled:

```text
VOID
```

or future:

```text
REVERSAL
```

workflow.

This preserves accountability.

---

# 20. Printable Invoice

A posted invoice should be printable.

It should show:

- Business Name
- Invoice Number
- Date
- Customer
- Products
- Quantities
- Actual Selling Prices
- Total
- Paid
- Due

The customer-facing invoice does not necessarily need to display:

- Product cost.
- Minimum allowed price.
- Internal profit.

---

# 21. Payment Collection

The system should support:

```text
CASH
BANK
MOBILE BANKING
OTHER
```

Payments may be:

- Full payment.
- Partial payment.
- Later due payment.

Example:

```text
Invoice = ৳15,300

Received = ৳10,000

Outstanding = ৳5,300
```

---

# 22. Payment Security

Payment information must be validated on the backend.

Staff should not be able to:

- Submit negative payment.
- Allocate more money than was actually received.
- Allocate more payment than an invoice outstanding amount without an explicitly supported advance-payment flow.

Example:

```text
Payment Received = ৳5,000

Allocation Submitted = ৳8,000
```

Result:

```text
Rejected
```

---

# 23. Payment History

Each payment must remain a separate transaction.

Do not simply overwrite:

```text
Customer Due
```

Example:

```text
Invoice = ৳15,300

Payment #1 = ৳10,000

Payment #2 = ৳3,000
```

Current due:

```text
৳2,300
```

The owner should be able to see both payments separately.

---

# 24. Customer Statement

Customer statement should show:

| Date | Transaction | Debit | Credit | Balance |
|---|---|---:|---:|---:|
| Sep 12 | INV-001 | 15,300 | - | 15,300 |
| Sep 12 | Payment | - | 10,000 | 5,300 |
| Sep 15 | Payment | - | 3,000 | 2,300 |

The statement should provide:

- Total Sales
- Total Payments
- Outstanding Due

---

# 25. Gross Profit

Gross profit should use:

```text
Actual Selling Price
-
Actual Stock Cost
```

Example:

```text
Stock Cost = ৳180

Default Sale Price = ৳220

Negotiated Price = ৳205
```

Actual gross profit:

```text
205 - 180
=
৳25 per unit
```

The system must not use the default selling price when the negotiated sale price was lower.

---

# 26. Cost History

Old profit calculations must remain stable even when later stock arrives at a different cost.

Example:

```text
January Stock:
Cost = ৳180

March Stock:
Cost = ৳195
```

A January sale must continue to use the January cost.

---

# 27. Dashboard

Owner dashboard may show:

- Today's Sales
- Today's Collection
- Outstanding Due
- Current Stock
- Gross Profit

Example:

```text
Today's Sales        ৳85,000

Cash Collection      ৳67,000

Outstanding Due      ৳18,000

Gross Profit         ৳11,500
```

---

# 28. Reports

Version 1 reports:

- Daily Sales
- Monthly Sales
- Cash Collection
- Outstanding Due
- Customer Statement
- Current Stock
- Stock History
- Basic Gross Profit

Reports may support:

- Date From
- Date To
- Customer
- Product
- Variant

---

# 29. Auditability

Important records should contain information such as:

```text
created_by

created_at
```

where relevant.

Sensitive actions should also preserve:

```text
approved_by

voided_by

voided_at
```

where applicable.

The owner should be able to identify who performed important business actions.

---

# 30. Client-Side Trust Policy

The application must follow this rule:

> **Frontend validation improves user experience. Backend validation protects the business.**

The server must never blindly trust values submitted by the browser for:

- Selling Price
- Quantity
- Available Stock
- Invoice Total
- Discount
- Payment
- Due
- Permission
- User Role

All important business rules must be checked again on the server.

---

# 31. Important Edge Cases

The system must correctly handle at minimum:

### Insufficient Stock

```text
Available = 5
Requested = 10

→ Reject
```

### Zero or Negative Quantity

```text
Quantity = 0 / -5

→ Reject
```

### Unauthorized Low Price

```text
Minimum = ৳190
Submitted = ৳160

→ Reject or require authorized override
```

### Manipulated Invoice Total

Browser sends:

```text
Actual item total = ৳10,000
Submitted total = ৳5,000
```

Backend must ignore the submitted total and recalculate.

### Duplicate Invoice Submission

Repeated form submission should not create duplicate business transactions.

### Concurrent Stock Sale

Two simultaneous users must not make stock negative.

### Negative Payment

```text
Payment = -৳500

→ Reject
```

### Excess Invoice Allocation

Payment allocation cannot exceed valid available payment or invoice balance.

### Editing Posted Invoice

Staff attempts to change an already posted sale.

```text
→ Deny
```

### Unauthorized Page Access

Hiding an Owner menu is not enough.

If Staff manually opens:

```text
/admin/profit-report/
```

backend authorization must deny access.

### Deleted Product

A product used in historical invoices must not make old invoices disappear.

Products should normally be deactivated rather than hard-deleted.

---

# 32. Authentication

Use secure user authentication.

Requirements:

- User login.
- Secure password hashing.
- Server-side sessions.
- CSRF protection.
- Owner/Admin/Staff authorization.
- Server-side permission enforcement.

---

# 33. Non-Functional Requirements

## Usability

The application should remain simple enough for normal shop staff.

Common actions should require minimal steps.

## Performance

Target:

```text
Approximately 3–5 concurrent users
```

## Reliability

Important business transactions should be transactional and consistent.

## Security

Business-sensitive rules must be enforced server-side.

## Maintainability

The system should remain a modular monolith without unnecessary infrastructure.

---

# 34. Out of Scope

The following remain outside the initial BDT 25,000–30,000 scope:

- Full Accounting / General Ledger
- Balance Sheet
- Complete Profit & Loss
- VAT/Tax
- Purchase/Supplier Accounting
- Multiple Warehouses
- Payroll
- Mobile Application
- Complex multi-step approval workflow
- Enterprise fraud detection
- Biometric authorization
- Full immutable enterprise audit system
- Advanced analytics
- Payment gateway
- E-commerce
- SMS
- Barcode hardware integration

Basic operational controls and accountability are included, but enterprise-grade fraud-management infrastructure is not.

---

# 35. Technical Scope

The agreed technical direction is:

```text
Python
Django 5.2 LTS
PostgreSQL
Django Templates
HTMX
Alpine.js
Bulma
Docker Compose
Caddy
Single VPS
```

Architecture:

```text
Modular Monolith
```

---

# 36. Acceptance Criteria

The application will be considered functionally successful when:

1. Products and variants can be created.
2. Different electrical product variants can be represented without assuming one measurement type.
3. Stock can be added.
4. Stock history is maintained.
5. Negative stock is prevented.
6. Customer invoices can be created.
7. Multiple products can exist on one invoice.
8. Negotiated selling prices can be used.
9. Staff cannot bypass minimum-price rules through browser manipulation.
10. Backend recalculates invoice totals.
11. Stock deduction and invoice posting are atomic.
12. Concurrent sales cannot create negative stock.
13. Full and partial payments can be recorded.
14. Payment history is preserved.
15. Customer outstanding balance can be calculated.
16. Customer statements can be viewed.
17. Gross profit uses actual selling price and actual historical cost.
18. Posted transactions cannot be silently altered by unauthorized staff.
19. Sensitive pages enforce backend authorization.
20. Owner can identify the responsible user for important operational records.
21. Invoices can be printed.
22. Basic stock, sales, collection, due, and gross-profit reports are available.

---

# 37. Final Product Scope

The Version 1 system covers:

```text
Product
→ Variant
→ Stock
→ Invoice
→ Negotiated Price
→ Price Validation
→ Stock Deduction
→ Payment
→ Due
→ Customer Statement
→ Reports
```

with business controls around:

```text
Authorization

Price Manipulation

Negative Stock

Duplicate Submission

Concurrent Sale

Historical Editing

Payment Manipulation

Transaction Accountability
```

The system should not merely record what staff enters.

It should actively enforce the business rules required to keep inventory, sales, payments, and profit information trustworthy.