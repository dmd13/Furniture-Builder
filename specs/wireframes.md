# Dredge Design Quote Builder Wireframe Spec

This document is a build-ready wireframe spec for the internal quoting tool.

## 1) Screen map

1. **Quotes Dashboard** (`/quotes`)
2. **Quote Editor** (`/quotes/:id/edit`)
3. **Client Preview** (`/quotes/:id/preview`)
4. **Settings / Pricing Rules** (`/settings/pricing`)

---

## 2) Screen 1 — Quotes Dashboard

## Goal
- See pipeline status quickly and start/duplicate quotes in one click.

## Layout (desktop)

```text
┌───────────────────────────────────────────────────────────────────────────┐
│ Top Nav: Logo | Quotes | Settings | Search | User                         │
├───────────────────────────────────────────────────────────────────────────┤
│ Filters: [Status] [Project Type] [Collection] [Date Range] [High Value]   │
├───────────────────────────────────────────────────────────────────────────┤
│ Kanban Columns:                                                           │
│ ┌───────────┐ ┌───────────┐ ┌────────────┐ ┌───────────┐ ┌─────────────┐  │
│ │ Draft     │ │ Sent      │ │ Viewed     │ │ Approved  │ │ Expired     │  │
│ │ Q-104233  │ │ Q-104229  │ │ Q-104210   │ │ Q-104188  │ │ Q-104140    │  │
│ │ ...       │ │ ...       │ │ ...        │ │ ...       │ │ ...         │  │
│ └───────────┘ └───────────┘ └────────────┘ └───────────┘ └─────────────┘  │
├───────────────────────────────────────────────────────────────────────────┤
│ Bottom Action Bar: [+ New Quote] [Duplicate] [Revise] [Archive]           │
└───────────────────────────────────────────────────────────────────────────┘
```

## Required interactions
- Drag quote card between statuses.
- Duplicate selected quote with incremented version.
- Global search by quote ID, client name, product keywords.
- Badge warnings:
  - expiring in < 3 days
  - below-margin-floor (internal only)

## Quote card fields
- Quote ID and version (`Q-104233 v2`)
- Client name
- Total quote value
- Lead-time weeks
- Last activity timestamp

---

## 3) Screen 2 — Quote Editor

## Goal
- Configure line items rapidly while maintaining margin control.

## Layout (desktop split)

```text
┌───────────────────────────────┬───────────────────────────────────────────┐
│ Left: Quote Builder           │ Right: Live Summary                       │
├───────────────────────────────┼───────────────────────────────────────────┤
│ Header: Q-ID | Status | Save  │ Subtotal                                  │
│ Customer panel                │ Adjustments                               │
│ Project panel                 │ Tax + Shipping                             │
│ Line Items list              │ Total                                      │
│  - L-001 Thomas Coffee Table  │ Deposit Due                                │
│  - L-002 Edson Bench          │ Margin Panel (internal)                    │
│ [ + Add Catalog Item ]        │  - Estimated COGS                          │
│ [ + Add Custom Item ]         │  - Gross Margin %                          │
│                               │  - Floor Warning + Override                │
└───────────────────────────────┴───────────────────────────────────────────┘
```

## Line item card details
- Product family selector (Edson/Thomas/Egans/Myers/Custom)
- Base type selector (chair, stool, bench, table, etc.)
- Template SKU selector (optional for custom)
- Dimension inputs (L/W/H + seat fields conditionally)
- Material selector (Big Leaf Maple, Myrtle, Walnut, White Oak, Other)
- Metal finish selector (Matte Black/White/Clear/None/Other)
- Upholstery panel (shown for lounge/sofa and optional custom)
- Complexity tier selector (1/2/3)
- Quantity
- Pricing breakdown (base + adjustments + multiplier)

## Required interactions
- Real-time price recalculation on any value change.
- Inline validation for dimensional min/max constraints.
- Duplicate line item action.
- Collapse/expand line item cards.
- Quote-level project adjustments panel:
  - white glove delivery
  - installation
  - rush surcharge
  - trade discount
  - package discount

## Keyboard shortcuts
- `Cmd/Ctrl + S`: save draft
- `Cmd/Ctrl + D`: duplicate selected line item
- `Cmd/Ctrl + Enter`: send quote

---

## 4) Screen 3 — Client Preview

## Goal
- Present polished quote PDF/web view for client approval + deposit.

## Layout

```text
┌───────────────────────────────────────────────────────────────────────────┐
│ Branding Header: Dredge Design | Quote #Q-104233 | Expires 2026-03-20    │
├───────────────────────────────────────────────────────────────────────────┤
│ Client + Project Summary                                                   │
├───────────────────────────────────────────────────────────────────────────┤
│ Itemized Table:                                                            │
│ Item | Specs (dimensions/material/finish) | Qty | Unit | Line Total       │
├───────────────────────────────────────────────────────────────────────────┤
│ Payment Milestones | Lead Time | Terms | Exclusions                        │
├───────────────────────────────────────────────────────────────────────────┤
│ [Approve Quote] [Pay Deposit] [Request Revision]                           │
└───────────────────────────────────────────────────────────────────────────┘
```

## Required interactions
- Toggle “client web view” vs “print/PDF preview”.
- Approve flow must lock quote version and timestamp.
- “Request Revision” should create revision ticket on quote thread.

## PDF export requirements
- Include branding/logo and contact details.
- Hide `internal` fields (COGS, margin, override reason).
- Include signature/acceptance section.

---

## 5) Screen 4 — Settings / Pricing Rules

## Goal
- Let owner tune pricing in minutes without developer support.

## Sections
1. **Material upcharge table**
2. **Powder coat upcharge table**
3. **Labor rate and baseline hours by base type**
4. **Complexity multipliers (tier 1/2/3)**
5. **Margin floor + target + override policy**
6. **Terms defaults (validity window, deposit %, warranty text)**

## Layout

```text
┌───────────────────────────────────────────────────────────────────────────┐
│ Settings Tabs: Materials | Finishes | Labor | Margin Rules | Terms        │
├───────────────────────────────────────────────────────────────────────────┤
│ Editable tables and sliders                                                │
│ [ Save ] [ Save as New Rule Set ] [ Restore Previous Version ]            │
└───────────────────────────────────────────────────────────────────────────┘
```

## Required interactions
- Rule-set versioning with rollback.
- “Preview impact” sandbox: pick sample product and see pricing deltas.
- Last modified timestamp + modified by user.

---

## 6) API contract (minimal)

- `POST /api/quotes` — create draft quote
- `GET /api/quotes/:id` — fetch quote with all sections
- `PATCH /api/quotes/:id` — update quote fields
- `POST /api/quotes/:id/reprice` — server-side authoritative pricing
- `POST /api/quotes/:id/send` — send to client
- `POST /api/quotes/:id/approve` — client approval webhook/action
- `GET /api/settings/pricing-rules` — fetch active rule set
- `PATCH /api/settings/pricing-rules` — update/tune rules

All payloads should validate against `specs/quote-config.schema.json` where applicable.

---

## 7) Acceptance criteria for immediate handoff

- Create quote with at least 2 configured line items and get deterministic totals.
- Any invalid enum or missing required field is rejected by schema validation.
- Margin floor warning appears when gross margin falls below configured threshold.
- Client preview excludes internal margin/cost fields.
- Approved quote becomes immutable; revisions generate `version + 1`.
