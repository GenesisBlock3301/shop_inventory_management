# Database Schema — Simplified

## 1. Core Structure

```text
Category
   │
Brand
   │
   ▼
Product
   │
   ▼
ProductVariant
   │
   ├── UnitOfMeasure
   │
   └── InventoryLot
             │
             └── StockMovement


Customer
   │
   ▼
Invoice
   │
   ▼
InvoiceItem
   │
   ▼
InvoiceItemLotAllocation


Customer
   │
   ▼
Payment
   │
   ▼
PaymentAllocation
   │
   ▼
Invoice
```

---

# 2. Main Tables

## Product

Stores the common product information.

```text
id
name
brand_id
category_id
description
is_active
```

Example:

```text
Super Star LED Bulb
BBS Cable
Schneider MCB
```

---

## ProductVariant

Represents the actual sellable version/SKU.

```text
id
product_id
sku
variant_name
uom_id
specs JSONB
default_cost_price
default_sale_price
batch_tracking
is_active
```

Examples:

```text
LED Bulb
→ 12W / B22 / 6500K

MCB
→ 32A / 2P / C-Curve

Cable
→ 1.5mm² / 90m / Red
```

### Important

`variant_name` is not always wattage.

It represents the complete sellable configuration.

---

## UnitOfMeasure

Determines how quantity is measured.

```text
id
code
name
```

Examples:

```text
PCS
MTR
COIL
BOX
SET
```

---

# 3. Inventory

## InventoryLot

Represents stock received at a specific cost.

```text
id
variant_id
batch_no        NULLABLE
quantity_received
quantity_remaining
unit_cost
received_at
```

Example:

```text
LED 12W

Lot 1
50 pcs
Cost = 180

Lot 2
100 pcs
Cost = 195
```

This allows cost history to remain correct.

---

## StockMovement

Keeps stock history.

```text
id
lot_id
type
quantity
reference
created_by
created_at
```

Examples:

```text
STOCK_IN   +50
SALE       -10
```

---

# 4. Customer

## Customer

```text
id
name
phone
address
is_active
```

Current due should be calculated from invoices and payments rather than stored as the main source of truth.

---

# 5. Sales

## Invoice

```text
id
invoice_no
customer_id
invoice_date
total_amount
status
created_by
```

Statuses:

```text
DRAFT
POSTED
VOID
```

---

## InvoiceItem

Stores what was sold.

```text
id
invoice_id
variant_id
quantity
unit_price
line_total
```

Example:

```text
LED Bulb 12W
Qty = 10
Price = 220
Total = 2200
```

---

## InvoiceItemLotAllocation

Connects the sale to the actual stock lot.

```text
id
invoice_item_id
lot_id
quantity
unit_cost
```

Example:

```text
Sold 15 bulbs

10 from Lot A @ 180
5 from Lot B @ 195
```

This is what makes gross-profit calculation accurate.

---

# 6. Payments and Due

## Payment

Stores every cash/payment receipt.

```text
id
customer_id
amount
payment_date
payment_method
received_by
```

Methods:

```text
CASH
BANK
MOBILE_BANKING
```

---

## PaymentAllocation

Shows which invoice a payment settled.

```text
id
payment_id
invoice_id
amount
```

Example:

```text
Payment = 10,000

INV-001 → 5,000
INV-002 → 5,000
```

---

# 7. Authentication

Use Django's built-in User, Group and Permission system.

Main roles:

```text
Owner
Admin
Staff
```

No separate custom role system is required initially.

---

# 8. Final Table List

```text
User
Purpose: Stores application users such as Owner, Admin and Staff.

Group
Purpose: Groups users by role, such as Owner, Admin or Staff.

Permission
Purpose: Defines what actions a user or group is allowed to perform.


Category
Purpose: Organizes products into categories such as Lighting, Cable, Fan or Circuit Breaker.

Brand
Purpose: Stores product brands such as Super Star, BBS, Schneider or Walton.

UnitOfMeasure
Purpose: Defines how product quantity is measured, such as PCS, MTR, COIL, BOX or SET.


Product
Purpose: Stores the general product information, such as LED Bulb, Cable or MCB.

ProductVariant
Purpose: Stores the actual sellable SKU/configuration of a product, such as 12W Bulb or 32A/2P MCB.


InventoryLot
Purpose: Stores each received stock lot along with quantity, cost and optional batch number.

StockMovement
Purpose: Keeps the audit history of why stock increased or decreased, such as STOCK_IN or SALE.


Customer
Purpose: Stores customer/party information used for invoices, payments and due tracking.


Invoice
Purpose: Stores the main sales transaction, including customer, invoice number, date and total amount.

InvoiceItem
Purpose: Stores individual products, quantities and selling prices inside an invoice.

InvoiceItemLotAllocation
Purpose: Connects sold invoice items to the exact inventory lots they came from so stock and gross profit can be calculated correctly.


Payment
Purpose: Stores every payment received from a customer.

PaymentAllocation
Purpose: Records how a customer payment is applied against one or more invoices.
```

---

# 9. Final Relationship View

```text
Product
   ↓
ProductVariant
   ↓
InventoryLot
   ↓
StockMovement


Customer
   ↓
Invoice
   ↓
InvoiceItem
   ↓
InvoiceItemLotAllocation
   ↓
InventoryLot


Customer
   ↓
Payment
   ↓
PaymentAllocation
   ↓
Invoice
```

---

# 10. Core Design Rules

### Product

```text
Product = general item

ProductVariant = actual sellable SKU
```

Example:

```text
Product:
LED Bulb

Variants:
12W
15W
20W
```

or:

```text
Product:
MCB

Variants:
16A / 1P
32A / 2P
63A / 4P
```

### Stock

```text
InventoryLot
= where stock came from and its cost

StockMovement
= why stock increased/decreased
```

### Sales

```text
Invoice
= sale header

InvoiceItem
= sold products
```

### Money

```text
Payment
= money received

PaymentAllocation
= where that money was applied
```

### Calculations

```text
Current Stock
= remaining inventory lots

Customer Due
= invoice total - allocated payments

Gross Profit
= sales - actual lot cost
```

This is the minimum clean schema I would lock for Version 1.