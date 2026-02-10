# Dredge Design Custom Furniture Quote Configurator

This repository contains a practical, build-ready package for a **daily-use quote builder** tailored to Dredge Design’s catalog and custom workflow.

## Deliverables in this repo

- `specs/quote-config.schema.json` — concrete JSON Schema (Draft 2020-12) for quote data, including customer/project fields, line-item configuration, pricing, terms, and internal margin controls.
- `specs/wireframes.md` — implementation wireframe spec with screen maps, required interactions, API surface, and acceptance criteria.

## Context snapshot (from dredge.design)

The quote-builder model is aligned to Dredge’s visible catalog and option patterns:

- Product families include seating, tables, benches, and casegoods under collections such as Edson, Thomas, Egans, and Myers.
- Common configurable options include wood species (Big Leaf Maple, Myrtle, Walnut, White Oak), powder-coat finishes (matte black/white/clear), dimensional specs, and upholstery on select pieces.
- Price bands span entry items through premium upholstered statement pieces.

## Immediate handoff path

1. Implement API payload validation against `specs/quote-config.schema.json`.
2. Build the four core screens in `specs/wireframes.md`:
   - Quotes Dashboard
   - Quote Editor
   - Client Preview
   - Settings/Pricing Rules
3. Wire the server-side repricing endpoint (`POST /api/quotes/:id/reprice`) to enforce margin and floor guardrails.
4. Ship MVP with deterministic totals + quote versioning.

## Next step (optional)

If needed, the next artifact can be a low-fidelity clickable prototype (Figma-ready frame map) that mirrors `specs/wireframes.md` 1:1.
