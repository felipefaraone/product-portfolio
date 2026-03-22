# Integrations Ecosystem — Pitch

## Problem

Lead generation and distribution platforms rely on third-party data validation services to maintain lead quality and ensure compliance with FCC and TCPA regulations. These integrations are not optional — they are the difference between a client staying compliant and facing litigation.

LeadExec had four specific problems:

**Lead quality:** Without robust validation, poor-quality leads reached clients, damaging trust and revenue. There was no native way to filter invalid or risky leads before delivery.

**Regulatory compliance:** Clients in insurance, home services, and finance operated under strict TCPA rules. Inadequate compliance checks exposed both LeadExec and its clients to legal and financial risk.

**Fragmented management:** The integrations page was a static list of toggles and API key fields. All actual configuration (field mapping, criteria, response handling) had to be repeated at the individual campaign level — for every single campaign.

**No processing logic:** There was no mechanism to process integrations in a sequential, configurable order. No way to say "run DNC check first, and if it fails, skip everything else to save cost."

## Opportunity

Competing platforms (LeadProsper, Boberdoo, LeadsPedia) already offered integration management, but none had solved the deeper architectural problem: centralised configuration that eliminates repetitive per-campaign setup. Their structural advantage came from reusable campaign objects, not from integration architecture.

This meant LeadExec could leapfrog competitors by building Lead Type-level integration management — true centralisation of field logic that no competitor had implemented.

## Solution Architecture

### Lead Type Inheritance Model

The critical architectural innovation. Instead of configuring integrations per campaign, configuration happens at the Lead Type level:

1. User enables an integration and enters API key at the system level
2. System auto-creates standardised response fields as global fields
3. User selects a Lead Type and configures: request mappings (with optional AI auto-map), response fields (choose from auto-created set), and integration criteria (e.g., reject leads with phone score below 80)
4. Settings apply automatically to all campaigns using that Lead Type
5. Individual campaigns can override both mappings and criteria if needed

The inheritance model uses a "disable and add" pattern for campaign-level customisation: campaigns cannot edit inherited criteria, but can disable specific inherited rules and add campaign-specific ones. This keeps the Lead Type as a clean single source of truth while preserving granular control.

### Sequential Validation Processing

Integrations execute in a user-defined order during the Post phase. Each step in the chain can be configured to stop or continue on failure. If PureCallerID rejects a lead and "skip remaining integrations" is enabled, subsequent calls (DNC.com, Trestle, etc.) are bypassed — saving cost and processing time.

### Compliance Integration Suite

Six integrations, each addressing a specific compliance or quality signal:

- **PureCallerID** — Phone validation via Aegis API. Validates phone numbers and flags compliance risks (inactive, DNC, TCPA litigation). Driven by AHS enterprise requirements.
- **TrustedForm** — Consent certificate validation. Verifies that leads have valid consent certificates, critical for TCPA compliance in regulated verticals.
- **Trestle** — Real Contact API. Validates phone numbers, email addresses, and physical addresses against authoritative data sources.
- **DNC.com** — Do Not Call registry scrubbing plus Litigator Scrub. Detects potential TCPA litigants and ensures DNC compliance before lead delivery.
- **IPQS** — IPQualityScore fraud scoring. Real-time phone validation with fraud score (0-100), VOIP detection, prepaid detection, and recent abuse signals.
- **Abstract API** — Email validation. Verifies email deliverability and format correctness.

### Integrations Page Redesign

Redesigned from a static toggle list to a centralised configuration hub:
- Integration cards with enable/disable, API key management, and "Configure" action
- Per-Lead-Type configuration flow with request mapping, response field selection, and criteria setup
- Visual indicators distinguishing inherited defaults from campaign-level overrides
- Phase-based delivery to manage complexity and avoid blocking merges

## Competitive Analysis

**LeadProsper:** Allows one campaign to serve multiple lead sources. Reduces campaign sprawl but does not eliminate field mapping duplication — users still configure mappings per campaign.

**Boberdoo:** One source can feed into multiple campaigns. Filter sets and partner configurations reduce distribution complexity. Still no inheritance or global settings.

**LeadsPedia:** Uses "Offers" as a higher-level container tying together sources and campaigns. Integration rules at Offer level apply broadly. Closest to centralisation, but still campaign-bound for mapping.

**LeadExec with this approach:** Lead Type-level management eliminates mapping redundancy that all competitors leave unsolved. Configure once, inherit everywhere. Campaign-level overrides preserve flexibility. This positions LeadExec ahead rather than catching up.

---

## Next Steps

To understand how we discovered and validated these decisions, see [02-discovery.md](./02-discovery.md). For details on how we executed this architecture, see [04-delivery.md](./04-delivery.md).
