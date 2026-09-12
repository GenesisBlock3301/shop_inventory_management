# Shop Inventory & Sales Management System

A web-based business management application for managing products, stock, sales invoices,
customer payments, and outstanding dues for a small retail/wholesale shop.

This repository currently holds the **planning and design documentation**. Implementation
has not started yet.

## Core Workflow

```
Stock Entry → Invoice Creation → Sale → Cash Collection → Due Tracking → Stock & Sales Reporting
```

## What the System Manages

- Products, variants, categories, and brands
- Units of measure and batch-based stock entry
- Stock movement and negative-stock prevention
- Sales invoices with atomic posting and concurrency protection
- Negotiated selling prices with minimum-price and override controls
- Customer payments, outstanding dues, and customer statements
- Sales, stock, and gross-profit reporting
- Staff access control and business-manipulation safeguards

## Technology Stack

| Layer | Technology |
|---|---|
| Language | Python |
| Backend | Django 5.2 LTS |
| Architecture | Modular Monolith |
| Frontend Rendering | Django Templates |
| Server-side Interactivity | HTMX |
| Client-side UI State | Alpine.js |
| CSS/UI Framework | Bulma |
| Database | PostgreSQL |
| Authentication | Django Authentication |
| Authorization | Django Groups + Permissions |
| Application Server | Gunicorn |
| Reverse Proxy | Caddy |
| Containerization | Docker + Docker Compose |
| Hosting | Single VPS |

## Documentation

| Document | Description |
|---|---|
| [Product Requirements Document](Product%20Requirements%20Document%20%E2%80%94%20Inventory%20%26%20Sales%20Management%20System.md) | Full functional requirements, user roles, business rules, and security constraints |
| [System Architecture](System%20Architecture%20%E2%80%94%20Inventory%20%26%20Sales%20Management%20System.md) | Architecture decisions, module structure, data model, and transaction design |
| [Database Schema — Simplified](Database%20Schema%20%E2%80%94%20Simplified.md) | Simplified entity relationships and core table structure |

## User Roles

- **Owner** — full business control, price and financial oversight
- **Admin** — day-to-day management within owner-defined limits
- **Staff** — operational tasks only (stock entry, invoicing, collection)

A primary design objective is preventing unauthorized manipulation of prices, stock,
invoices, and payments by operational users.

## Status

📋 Planning / Design phase — documentation only, no code yet.
