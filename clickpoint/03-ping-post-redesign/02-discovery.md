# Ping/Post Configuration Redesign — Discovery

## How Ping/Post Works Today

In LeadExec, Delivery configuration is edited through the Delivery tab inside the client's Delivery Account. Under normal conditions, the structure is clean: Primary Delivery Method, Additional Delivery Method #1, Additional Delivery Method #2. Everything is visible and consistent.

Ping/Post breaks this pattern dramatically.

### The configuration flow users must follow today

1. Create a Ping delivery method (configured as an HTTP Webhook)
2. Create a Post delivery method (configured as PING/POST type)
3. Configure each independently: URL endpoints, authentication, mappings, request bodies
4. Link them inside the Delivery Tab by selecting the Ping method from a special dropdown
5. Extract the Ping reference ID using "Ping Reference Search" — a regex-based search on the Ping response
6. Store the reference ID in a custom lead field
7. Include that field in the Post request body

### Where the UX breaks

**Delivery Tab inconsistency:** When Post (Ping/Post) is selected as the primary method, a new "PING Delivery Method" dropdown appears, and Additional Delivery Method #1 disappears entirely. The system internally uses that slot for the Ping, but the user sees only "#2" with no explanation. Users frequently ask Support why the first additional method is missing.

**Delivery Methods list fragmentation:** The user sees separate entries — Ping (HTTP Webhook), Ping Post (PING/POST), and sometimes an extra Post method. They know they are configuring a single Ping/Post flow, but LeadExec forces them to work in two independent objects.

**Duplicated configuration:** Ping and Post share nearly identical structure (URL Endpoint, Authentication, Mappings, Request Body, Response Settings). Authentication is identical in 90% of cases. Users must configure everything twice.

**Ping Reference Search:** The most technical step. Users must understand response structures, define search expressions, create lead fields, and then manually reference that field in the Post request. Error-prone and support-intensive.

### Backend constraint

The lead engineer confirmed that the backend routing for Ping/Post is deeply embedded in the distribution engine, sensitive to sequencing logic, and not feasible to rewrite safely without major system-breaking risk.

**Therefore:** Keep backend behaviour exactly the same. Combine Ping + Post into a single unified UI editor. This creates a dramatically simpler UX with zero backend risk.

---

## Proposed Solution: Unified Ping/Post Delivery Editor

A new UI component where:
- Ping settings appear in one structured section
- Post settings appear in a second structured section
- Shared settings (notifications, schedule, permissions) appear once
- The Delivery Tab no longer hides Additional Method #1
- Ping/Post configuration feels like one workflow
- Backend still stores two delivery methods exactly as today

The UI creates or updates two backend objects (Ping as HTTP Webhook, Post as PING/POST) with an auto-generated naming convention: [Name] – Ping, [Name] – Post. No modification to routing logic. No impact on the distribution engine.

---

## Design Requirements

**Smart defaulting for Ping Reference:** When the user defines Ping Reference Search, automatically create the lead field if missing and auto-map it into the Post body. Removes the need for manual double configuration.

**Delivery Tab changes:** Remove the confusing "Ping Delivery Method" dropdown. Remove the "Additional Delivery Method #1/#2" numbering confusion. Show additional methods after Post succeeds with clearer labels ("Send additional delivery after successful Post").

**"Same as PING" pattern:** For Content Type, Timeout, Custom Headers, and Authentication, POST shows the inherited value from PING as context. User can override explicitly or reset to the inherited default. For Request Bodies: always separate (Ping sends minimal fields; Post sends full payload).

**"Include from PING" for mappings:** When enabled, field mappings added in PING are automatically created in POST. POST-specific additions and overrides remain available.

---

## Competitive Benchmarking: LeadProsper

Reviewed LeadProsper's Ping/Post buyer setup video. Items they handle that we should consider for the next iteration:

1. **Payload type lock-in warning** — warns if switching format will reset the builder
2. **Explicit test mode** — uses a test flag in the payload with a go-live checklist
3. **Duplicate outcome mapping** — POST response maps Accepted, Duplicated, and Failed as first-class outcomes
4. **Bid threshold rule** — "send POST only if bid is greater than X"
5. **Ping reference insertion token** — simple template token to pull ping_id into post payload
6. **Payload transformers** — formatting transformers (e.g., phone formatting) to match buyer requirements
7. **Test output copy** — copy full request/response after successful test to share with buyer

LeadProsper also makes required fields more visually obvious and explicitly states that PING should not include PII — a useful guardrail we could add.

---

## Open Questions (Resolved During Build)

1. **Ping Reference Search on POST Response Settings** — Does it make sense in the POST phase? Likely legacy/copied behaviour. Decision required: keep vs remove.
2. **Auto-passing of Ping reference ID** — Can the unified UI automatically pass it from Ping to Post, or does manual mapping remain required in the MVP?
3. **URL Redirect Search on Ping** — Is redirect typically used during Ping? Keep unless proven otherwise.
4. **Express ID mapping enforcement** — Required to save, required to activate, or informational only?
5. **Assistive tooltips** — Include in the MVP or defer? Example: "Ping during sort" is frequently misunderstood.
