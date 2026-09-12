# System Architecture

## 1. Architecture Decision

The system will use a **Modular Monolith Architecture**.

All core business functionality will run within one Django application and one PostgreSQL database, while business domains remain separated into logical Django apps/modules.

### Locked Technology Stack

| Layer | Technology |
|---|---|
| Language | Python |
| Backend | Django 5.2 LTS |
| Architecture | Modular Monolith |
| Frontend Rendering | Django Templates |
| Server-side Interactivity | HTMX |
| Client-side UI State | Alpine.js |
| CSS/UI Framework | Bulma |
| Additional JavaScript | Minimal Vanilla JavaScript |
| Database | PostgreSQL |
| ORM | Django ORM |
| Authentication | Django Authentication |
| Authorization | Django Groups + Permissions |
| Application Server | Gunicorn |
| Reverse Proxy | Caddy |
| Containerization | Docker |
| Deployment | Docker Compose |
| Hosting | Single VPS |
| Invoice Printing | HTML + Print CSS |
| Backup | Automated PostgreSQL Backup |

---

# 2. Why Modular Monolith

The system is expected to serve approximately 3–5 concurrent users.

The main business operations are tightly connected:

```text
Stock Entry
    ↓
Inventory
    ↓
Invoice
    ↓
Stock Deduction
    ↓
Payment
    ↓
Due
    ↓
Reports
```

These operations benefit from being handled inside the same application and database transaction boundary.

A microservice architecture would introduce unnecessary:

- API communication
- Distributed transactions
- Deployment complexity
- Authentication complexity
- Monitoring requirements
- Infrastructure cost
- Development time

Therefore, the system will remain a modular monolith unless future requirements provide a concrete reason to change.

---

# 3. High-Level Architecture

```text
                  Browser
                     │
                  HTTPS
                     │
                     ▼
              ┌─────────────┐
              │    Caddy    │
              └──────┬──────┘
                     │
                     ▼
              ┌─────────────┐
              │  Gunicorn   │
              └──────┬──────┘
                     │
                     ▼
      ┌─────────────────────────────┐
      │           Django            │
      │                             │
      │  Accounts                   │
      │  Customers                  │
      │  Products                   │
      │  Inventory                  │
      │  Sales                      │
      │  Payments                   │
      │  Reports                    │
      │  Dashboard                  │
      └──────────────┬──────────────┘
                     │
                 Django ORM
                     │
                     ▼
              ┌─────────────┐
              │ PostgreSQL  │
              └─────────────┘
```

PostgreSQL will remain the primary source of truth.

---

# 4. Django Application Structure

Recommended structure:

```text
inventory_system/
│
├── accounts/
├── customers/
├── products/
├── inventory/
├── sales/
├── payments/
├── reports/
├── dashboard/
├── core/
│
├── templates/
├── static/
└── config/
```

Each module will own its domain-specific models, services, queries, forms, views and templates.

Example:

```text
sales/
├── models.py
├── services.py
├── selectors.py
├── forms.py
├── views.py
├── urls.py
└── templates/
```

Business logic should not be placed directly inside large Django views.

Important operations should be represented through services such as:

```text
create_invoice(...)
receive_payment(...)
add_stock(...)
adjust_stock(...)
```

---

# 5. Frontend Architecture

The frontend stack is:

```text
Django Templates
        +
      Bulma
        +
       HTMX
        +
    Alpine.js
        +
Minimal Vanilla JavaScript
```

Each technology has a specific responsibility.

## Bulma

Responsible for:

- Layout
- Forms
- Tables
- Buttons
- Cards
- Navigation
- Pagination
- Responsive styling
- Basic visual components

## HTMX

Responsible for interactions requiring communication with Django.

Examples:

- Customer search
- Product search
- Loading pack sizes
- Loading batches
- Checking stock
- Server-side form submission
- Report filtering
- Pagination
- Loading partial content

Example flow:

```text
Select Product
      ↓
HTMX Request
      ↓
Django
      ↓
Available Pack Sizes
      ↓
HTML Fragment Returned
```

## Alpine.js

Responsible for lightweight browser-local state.

Examples:

- Add/remove invoice rows
- Modal state
- Dropdown state
- Show/hide sections
- Local calculations
- Confirmation UI
- Temporary form state

Example:

```text
Quantity = 10
Unit Price = 220

Alpine.js immediately displays:

Line Total = 2,200
```

The backend will still recalculate and validate the authoritative value.

## Vanilla JavaScript

Vanilla JavaScript should only be used where HTMX or Alpine do not provide an appropriate solution.

Large custom JavaScript state-management code should be avoided.

---

# 6. Why React / Next.js Is Not Used

React or Next.js would require additional architecture:

```text
React
   ↓
REST API
   ↓
Django
   ↓
PostgreSQL
```

This would introduce:

- Separate frontend project
- API contracts
- Authentication coordination
- More deployment work
- More state management
- More testing
- More code
- Increased development time

The current system does not have enough frontend complexity to justify that architecture.

If future requirements include highly complex client-side workflows, offline mode, rich POS functionality or a dedicated mobile application, this decision can be revisited.

---

# 7. Product Data Model

The product hierarchy will be:

```text
Product
   │
   └── ProductVariant
           │
           └── InventoryLot
```

Example:

```text
Powder A
│
├── 500g
│    ├── Batch B001
│    └── Batch B002
│
└── 1kg
     └── Batch B003
```

This enables different:

- Pack sizes
- Batch numbers
- Cost prices
- Selling prices
- Stock quantities

without duplicating the entire product record.

---

# 8. Core Entities

Major database entities:

```text
User

Customer

Product
ProductVariant
InventoryLot

StockMovement

Invoice
InvoiceItem
InvoiceItemLotAllocation

Payment
PaymentAllocation
```

Relationship overview:

```text
Product
   │
   └── ProductVariant
           │
           └── InventoryLot
                   │
                   └── StockMovement


Customer
   ├── Invoice
   │      │
   │      └── InvoiceItem
   │             │
   │             └── InvoiceItemLotAllocation
   │
   └── Payment
          │
          └── PaymentAllocation
                 │
                 └── Invoice
```

---

# 9. Inventory Architecture

Current stock must not exist without an underlying movement history.

## InventoryLot

Example fields:

```text
id
variant_id
batch_no
quantity_received
quantity_remaining
unit_cost
received_at
```

## StockMovement

Example:

```text
id
lot_id
movement_type
quantity
reference_type
reference_id
created_by
created_at
```

Possible movement types:

```text
STOCK_IN
SALE
ADJUSTMENT
```

Example:

```text
Inventory Lot B001

STOCK_IN     +100
SALE          -15
SALE          -10
------------------
Current        75
```

`quantity_remaining` may be stored for efficient reads, while `StockMovement` provides the audit trail explaining how that quantity was reached.

---

# 10. Invoice Architecture

## Invoice

Important fields:

```text
id
invoice_number
customer_id
invoice_date
subtotal
total_amount
lifecycle_status
created_by
created_at
```

Lifecycle statuses:

```text
DRAFT
POSTED
VOID
```

Payment status is derived for posted invoices and is not a lifecycle state or an
editable field:

```text
UNPAID   = allocated payments are 0
PARTIAL  = allocated payments are greater than 0 and less than invoice total
PAID     = allocated payments equal invoice total
```

## InvoiceItem

```text
id
invoice_id
variant_id
quantity
unit_price
line_total
```

`unit_price` is a historical snapshot. Historical cost is preserved by the
`InvoiceItemLotAllocation` records that link each sold quantity to its
`InventoryLot`.

For example:

```text
Current Inventory Lot Cost = 180

Invoice Created:
unit_price = 220
```

If the batch cost later becomes BDT 195, the old invoice must continue using BDT 180 when calculating historical gross profit.

---

# 11. Atomic Invoice Transaction

Creating an invoice must be an atomic database operation.

Conceptually:

```text
BEGIN TRANSACTION

1. Validate customer
2. Validate items
3. Lock relevant batch rows
4. Check available stock
5. Create invoice
6. Create invoice items
7. Deduct stock
8. Create StockMovement records
9. Record initial payment
10. Calculate invoice status and due

COMMIT
```

If any step fails:

```text
ROLLBACK
```

This prevents partial invoices or incorrect stock quantities.

---

# 12. Concurrent Stock Protection

Suppose:

```text
Available stock = 10
```

Two staff members simultaneously attempt:

```text
User A → Sell 8
User B → Sell 7
```

Both operations must not succeed.

The application should use PostgreSQL transactions and row-level locking where required to serialize stock-sensitive operations.

Negative stock must never occur because of concurrent invoice creation.

---

# 13. Payment Architecture

Payments must be stored as individual transaction records.

## Payment

```text
id
customer_id
invoice_id
amount
payment_date
payment_method
reference
received_by
created_at
```

Example:

```text
Invoice Total = 10,000

Payment #1 = 4,000
Payment #2 = 2,500
```

Result:

```text
Total Paid = 6,500
Current Due = 3,500
```

The system should not simply overwrite a single payment value because historical payment information is required.

---

# 14. Customer Due Architecture

`Customer.current_due` should not be treated as the authoritative financial record.

Outstanding receivable should come from transactional data.

Conceptually:

```text
Outstanding
=
Invoice Value
-
Allocated Payments
```

A cached balance may be introduced later if necessary, but invoices and payments remain the source of truth.

---

# 15. Customer Statement

Customer statements will combine invoice and payment transactions.

Example:

| Date | Type | Reference | Debit | Credit | Balance |
|---|---|---|---:|---:|---:|
| Sep 01 | Invoice | INV-001 | 10,000 | 0 | 10,000 |
| Sep 02 | Payment | PAY-001 | 0 | 6,000 | 4,000 |
| Sep 10 | Invoice | INV-015 | 5,000 | 0 | 9,000 |
| Sep 12 | Payment | PAY-008 | 0 | 3,000 | 6,000 |

This provides receivable tracking without implementing a complete accounting General Ledger.

---

# 16. Gross Profit

Historical gross profit should be calculated from invoice items.

```text
Gross Profit
=
(Unit Selling Price - Unit Cost)
× Quantity
```

Example:

```text
Cost = 180
Price = 220
Quantity = 100

Gross Profit
= (220 - 180) × 100
= 4,000
```

Current product cost must never be used to recalculate old invoice profits.

---

# 17. Reporting Architecture

Reports will initially query PostgreSQL directly through Django ORM/query services.

No data warehouse or analytics database is required.

Initial reports:

```text
Daily Sales
Monthly Sales

Daily Collection
Monthly Collection

Outstanding Due

Current Stock

Gross Profit

Customer Statement
```

Supported filters may include:

```text
Date From
Date To
Customer
Product
Batch
```

Indexes should be introduced for frequently filtered columns.

---

# 18. Authentication Architecture

The system will use **Django's built-in authentication system**.

A custom user model based on `AbstractUser` should be created from the beginning.

Conceptually:

```text
User
├── Owner
├── Admin
└── Staff
```

Django Groups and Permissions should be used for authorization.

Example:

```text
Staff

✓ View stock
✓ Create invoice
✓ Receive payment
✓ View customer statement

✗ Manage users
✗ Access administrative configuration
✗ Modify protected historical transactions
```

Owner/Admin permissions may include all operational functionality.

Authentication answers:

```text
Who is the user?
```

Authorization answers:

```text
What can that user do?
```

Backend permission checks must be enforced even when the corresponding button or menu is hidden from the frontend.

---

# 19. Django Admin

Django Admin will be used only for internal administration.

Appropriate uses:

- User management
- Emergency data inspection
- Administrative configuration
- Developer/support troubleshooting

Daily business workflows should use custom interfaces.

For example:

```text
Stock Entry
Invoice Creation
Cash Collection
Customer Statement
Reports
```

should not rely primarily on Django Admin.

---

# 20. API Strategy

Version 1 does not require a public REST API.

Business logic should nevertheless remain independent from HTML views.

For example:

```text
sales/
├── models.py
├── services.py
├── selectors.py
├── forms.py
├── views.py
└── urls.py
```

This makes future REST API development possible without rewriting the core domain logic.

Possible future consumers:

```text
Mobile App
External Integration
Barcode/POS Client
Third-party System
```

---

# 21. Deployment Architecture

Production deployment:

```text
Internet
   │
 HTTPS
   │
   ▼
 Caddy
   │
   ▼
Gunicorn
   │
   ▼
 Django
   │
   ▼
PostgreSQL
```

Docker Compose:

```text
services:

web
database
reverse-proxy
```

All components can initially run on one VPS.

---

# 22. VPS Specification

Recommended production server:

```text
2 vCPU

4 GB RAM

40–80 GB SSD
```

This is more than sufficient for the expected 3–5 concurrent operational users while providing comfortable headroom for:

- Linux
- Docker
- Django
- Gunicorn
- PostgreSQL
- Caddy
- Backups

A smaller 1 vCPU / 2 GB instance may technically operate the system but provides less operational headroom.

---

# 23. Backup Architecture

Financial and inventory data must be backed up automatically.

Minimum policy:

```text
Daily PostgreSQL backup
```

Recommended retention:

```text
7 daily backups
+
4 weekly backups
```

At least one backup copy should preferably exist outside the production VPS.

Critical data includes:

- Invoices
- Payments
- Customer dues
- Products
- Stock movements
- Batch information

Backup reliability is more important for this system than high-availability infrastructure.

---

# 24. Security

Minimum security requirements:

- HTTPS
- Secure Django session authentication
- Django password hashing
- CSRF protection
- Server-side validation
- Authorization enforcement
- Restricted Django Admin
- Environment-based secrets
- Database credentials isolated from source code
- Automated backups
- Secure cookies in production
- Production debug mode disabled

---

# 25. Business Invariants

Important business rules must be enforced on the backend.

## Inventory

```text
stock >= 0

sale_quantity > 0

sale_quantity <= available_stock
```

## Payment

```text
payment > 0

payment <= outstanding_due
```

unless an explicit overpayment workflow is later introduced.

## Product

```text
cost_price >= 0

selling_price >= 0
```

## Invoice

```text
quantity > 0

unit_price >= 0

invoice_total >= 0
```

Frontend validation is useful for UX, but backend validation remains authoritative.

---

# 26. Database Indexing

Likely initial indexes include:

```text
invoice_number
invoice_date

customer_id

batch_number
product_variant_id

payment_date

stock_movement.created_at
```

Additional indexes should only be introduced based on actual query patterns and measured performance.

---

# 27. Explicitly Excluded Infrastructure

Version 1 does not require:

```text
Microservices
Kubernetes
Redis
Celery
Kafka
RabbitMQ
Elasticsearch
WebSockets
API Gateway
Separate frontend deployment
Separate database server
Event-driven architecture
```

These technologies should only be introduced when a real requirement justifies their operational cost.

---

# 28. Future Expansion

The architecture can later accommodate:

```text
REST API
Mobile Application
Purchase Management
Supplier Management
Expenses
Sales Returns
Multiple Warehouses
Barcode/POS Integration
VAT/Tax
Full Accounting
Advanced Reporting
Background Tasks
Notifications
Redis
Celery
Object Storage
```

Future additions should extend the modular monolith first unless scaling or organizational requirements justify extraction into independent services.

---

# 29. Final Locked Architecture

The v1 architecture is:

```text
Python
+
Django 5.2 LTS
+
Django Templates
+
HTMX
+
Alpine.js
+
Bulma
+
PostgreSQL
+
Gunicorn
+
Caddy
+
Docker Compose
+
Single VPS
```

Architecture style:

**Modular Monolith + Relational Database**

Recommended infrastructure:

**2 vCPU + 4 GB RAM + 40–80 GB SSD**

Authentication:

**Django Auth + Custom AbstractUser + Groups + Permissions**

Frontend responsibility:

```text
Bulma
→ Styling and layout

HTMX
→ Server-driven interaction

Alpine.js
→ Lightweight client-side state

Vanilla JS
→ Exceptional browser-specific behavior only
```

Engineering priority:

1. Correct batch-wise inventory.
2. Atomic invoice and stock transactions.
3. Reliable payment and due history.
4. Immutable historical prices and costs.
5. Accurate customer statements.
6. Backend-enforced permissions.
7. Automated database backups.
8. Simple and efficient operational UI.

This architecture is now the **baseline for Version 1**. Changes should only be made when a concrete product or operational requirement justifies them.
