# Ping/Post Configuration Redesign — MVP Pitch

## Problem

Setting up Ping/Post delivery in LeadExec requires configuring two separate delivery methods and manually connecting them. This causes confusion during onboarding, duplicated work across authentication, headers, and mappings, and makes troubleshooting difficult because the full Ping-to-Post flow is never visible in one place.

The specific UX breakdowns:
- Ping and Post are conceptually one workflow, but the UI splits them into two separate areas
- The Delivery Tab hides "Additional Delivery Method #1" when Ping/Post is selected, causing structural inconsistency that confuses users
- A "Ping Delivery Method" dropdown appears only for Ping/Post and breaks the mental model of how Delivery Methods normally work
- Users must manually extract a Ping reference value, store it in a lead field, and then include it in the Post request body — an extremely technical step
- Authentication, mappings, and request body templates must be configured twice in 90% of cases where they are identical

## Constraint

The lead engineer confirmed that backend Ping/Post routing is legacy code, deeply embedded in the distribution engine, sensitive to sequencing logic, and not feasible to rewrite safely. Any solution must be UI-only.

## Solution

A unified Ping/Post configuration editor that presents both steps in a single guided interface while the backend continues operating as two separate delivery methods. The UI maps one editing experience to two internal objects with zero backend changes.

### MVP Scope (In)

**Unified editor** with explicit PING and POST sections showing sequential order (PING first, POST second).

**Shared sections configured once:** General, Portal Permissions, Delivery Schedule, Notifications.

**"Same as PING" reuse options for POST:**
- Content Type, Timeout: option to inherit from PING with visible inherited value
- Custom Headers: "Include from PING" option
- Authentication Type: "Same as PING" option
- Mappings: "Include from PING" checkbox that writes to POST when adding mappings on PING side

**Response Settings simplification:** JSON and XML use Key + Value matching. Text/HTML uses Search Pattern with per-field regex. Regex toggle defaults to Yes.

**Express ID mapping:** POST mappings include explicit prompt/placeholder for Express ID (Ping reference) mapping.

**Unsaved changes indicators** and confirmation flow on attempted close.

### MVP Scope (Out)

- Entry point selector redesign (dropdown to selector with descriptions and quick search)
- Bulk Add redesign (card layout to table layout)
- AI-driven request body generation
- Consolidated mappings view (Ping only / Post only / Both)
- Auto-passing of Ping reference ID into Post (requires engineering validation)

### New Clarifications in MVP

**1. Inherited value visibility.** When POST inherits from PING, the UI shows the actual value as context. Override replaces it explicitly with a "Reset to default" action. Rationale: avoids hidden state and reduces troubleshooting time.

**2. Include from PING writes to POST.** When enabled, adding a field mapping in PING also creates it in POST, while still allowing POST-specific additions or overrides. Rationale: matches the most common workflow where POST includes everything from PING plus additional fields.

**3. Request bodies remain separate.** "Same as PING" does not apply to request bodies. Ping sends minimal fields for bid evaluation; Post sends the full lead payload. These are almost always different.

## Delivery

Three spec iterations produced (v1, v2, v3) with progressive refinement. Delivered by the engineer at 134 hours. Feature-flagged deployment with incremental rollout.

## Competitive Reference

LeadProsper Ping/Post setup was benchmarked, identifying 7 capabilities for the next iteration: payload type lock-in warning, explicit test mode with go-live checklist, duplicate outcome mapping, bid threshold rules, simple ping reference insertion token, payload transformers, and test output copy/share functionality.

## Next Iteration Direction

- Entry point selector redesign
- Bulk Add table layout
- AI-generated request bodies
- Auto-suggestion for Ping reference handling (guidance, not silent automation)
- Request body editor autosuggestion
