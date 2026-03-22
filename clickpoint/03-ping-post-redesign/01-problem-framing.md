# Ping/Post Configuration Redesign — Problem Framing

## The Problem

Ping/Post is the dominant delivery method in the lead distribution industry — a two-step auction where a Ping sends limited lead data for bid evaluation, and a Post delivers the full lead to the winning buyer. Conceptually, it is a single workflow, but LeadExec forced users to configure it as two completely separate delivery methods.

---

## What Users Had to Do

1. Create a Ping (as an HTTP Webhook)
2. Create a Post (as a PING/POST method)
3. Configure each independently: URL endpoints, authentication, mappings, request bodies
4. Manually link them inside the Delivery Account tab
5. Extract a Ping reference ID using "Ping Reference Search" expression
6. Store it in a custom lead field
7. Inject it into the Post request body

Authentication and headers were duplicated in 90% of cases. This duplication was not just tedious — it was error-prone and confusing.

---

## The UX Breakdown

### The Delivery Tab Inconsistency

When Post (Ping/Post) was selected as the primary method, something strange happened: "Additional Delivery Method #1" disappeared entirely from the UI. The system internally used that slot for the Ping, but users only saw "#2" visible with no explanation.

This was the single most confusing element of LeadExec's configuration experience. New customers frequently got lost during onboarding and demos. Support repeatedly fielded questions: "Why is the first additional method missing?"

### Fragmented Delivery Methods List

Users saw separate entries — Ping (HTTP Webhook), Ping Post (PING/POST), and sometimes an extra Post method. They knew they were configuring a single Ping/Post flow, but LeadExec forced them to work in two independent objects.

### Duplicated Configuration

Ping and Post shared nearly identical structure: URL Endpoint, Authentication, Mappings, Request Body, Response Settings. Authentication was identical in 90% of cases. Users had to configure everything twice.

### The Technical Step: Ping Reference Search

The most error-prone part. Users had to:
- Understand response structures
- Define search expressions via regex
- Create custom lead fields
- Manually reference that field in the Post request

This was support-intensive and frequently misunderstood.

---

## The Backend Constraint

The lead engineer confirmed that the backend routing for Ping/Post is deeply embedded in the distribution engine, sensitive to sequencing logic, and not feasible to rewrite safely without major system-breaking risk.

Any solution had to be **UI-only**. The backend would continue operating exactly as today — creating and updating two separate delivery methods. The unified editor would be a presentation layer only.
