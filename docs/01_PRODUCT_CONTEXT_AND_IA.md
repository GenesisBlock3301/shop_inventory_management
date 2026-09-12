# 01 — Product Context + Information Architecture

## Product
Inventory & Sales Management System for daily operational use by staff and owner/admin.

## Main UX goals
- fast daily operation
- low cognitive load
- accurate stock handling
- simple sale/invoice/payment workflows
- clear due tracking
- owner visibility
- internal accounting accuracy without exposing technical implementation

## Primary users

### Staff
Typical jobs:
- check stock
- add stock
- create sale
- select customer
- add products/quantity
- receive full or partial payment
- print invoice
- collect due
- process returns/corrections where allowed

### Owner/Admin
Typical jobs:
- review sales
- review stock
- review due/receivables
- inspect customer statements
- reports
- sensitive corrections
- settings and permissions

## User-facing business concepts
Use these in UI:
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
- Settings

## Internal backend concepts
These may exist in DB/backend but should not appear as raw UI concepts:

### InventoryLot
Tracks stock received/produced at different times/costs.

Example:
- 10 pcs @ 180
- later 10 pcs @ 195

Useful for:
- COGS
- stock valuation
- returns
- traceability
- audit

Staff should see human-readable stock information, not `InventoryLot`.

### PaymentAllocation
Tracks how one payment settles one or more invoices.

Example:
- customer pays 5,000
- 3,000 → Invoice A
- 2,000 → Invoice B

Staff sees payment history, paid/due status, not raw allocation rows.

### InvoiceItemLotAllocation
Tracks which stock lots were consumed by each invoice item.

Useful for:
- COGS
- returns
- reversal
- auditability

This should remain hidden from normal staff UI.

## Core principle

> Database complexity must not become UI complexity.

---

# Information Architecture

## Main Navigation

### Dashboard
Possible summary:
- Today's Sales
- Outstanding Due
- Low Stock
- Recent Transactions

### Inventory
- Current Stock
- Stock In
- Stock History

Optional/admin:
- Stock Adjustment
- Product Setup

### Sales
- New Sale
- Invoices
- Sales Return

### Customers
- Customer List
- Customer Details
- Customer Statement
- Due Collection

### Reports
- Sales Report
- Stock Report
- Due / Receivable Report

### Settings
Admin-oriented configuration.

## Navigation rule
Navigation should reflect the user's mental model, not the database schema.

Bad:
- InventoryLot
- PaymentAllocation
- InvoiceItemLotAllocation

Good:
- Stock History
- Payment History
- Customer Statement
- Invoice Details

## Role visibility

### Staff
Usually visible:
- Dashboard
- Inventory
- Sales
- Customers
- selected reports

Usually restricted:
- sensitive adjustments
- product configuration
- permission management
- destructive corrections

### Owner/Admin
Usually visible:
- all operational screens
- reports
- settings
- sensitive corrections
- permission/configuration

Exact permissions must follow the PRD.
