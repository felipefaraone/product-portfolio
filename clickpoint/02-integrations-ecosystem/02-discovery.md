# Integrations Ecosystem — Discovery

This document traces what we learned during the discovery phase, from the initial competitive analysis through the Lead Type inheritance debate and the page redesign process.

---

## Initial Architecture and First Integrations (Jan–Mar 2024)

### Starting point
LeadExec had no third-party validation system. The integrations page was a static list of toggles and API key fields with no configuration depth.

### Key early decisions

**Integration criteria management (Jan 15, 2024)**

Two options were presented to the lead engineer and the CEO:

- **Option A:** Manage criteria directly within each integration (the LeadProsper approach)
- **Option B:** Centralised management at the campaign level, integrating all criteria (lead fields and integration responses in one place)

Decision: **Option B.** Rationale: unified visibility for all campaign-level criteria, greater flexibility for mixing field-level and integration-level criteria, and support for complex scenarios like "if score below 60, reject; if between 60-80, send to QC."

The lead engineer confirmed that Option B aligned better with the existing LeadExec architecture.

**Validation timing: Ping vs Post**

Validations would primarily occur during the Post phase. Reasoning:
- Faster Ping responses by limiting unnecessary checks (critical for real-time auction environments)
- Cost optimisation by halting sequential validations upon failures
- Client flexibility to choose between speed (parallel) and cost savings (sequential)

### First integrations shipped
PureCallerID was first — driven by AHS's enterprise compliance needs. Followed by Trestle. Both built on the same pattern: API discovery, field mapping configuration, criteria builder, sequential processing support. This pattern became the template for all subsequent integrations.

---

## Competitive Analysis and the Inheritance Debate (Aug 2024)

### The repetitive configuration problem
As more integrations shipped, the fundamental UX problem became acute. Users had to configure identical field mappings and criteria for every campaign, even when settings were identical across all campaigns under the same Lead Type. Enterprise clients with hundreds of campaigns were spending hours on what should take minutes.

### Three solutions evaluated

**Solution A: Lead Type-Level Management**
Move all mapping and criteria configuration up to the Lead Type level. Campaigns inherit settings automatically. Campaign-level overrides available for exceptions.

Benefits: Configure once, apply everywhere. Eliminates field discovery problem (response fields auto-created at Lead Type level). Enables AI-assisted auto-mapping. Standardised field naming for cross-campaign reporting.

Challenge: Significant development effort. Requires learning and potentially refactoring parts of Lead Type architecture.

**Solution B: Import Settings with Navigation Shortcut**
Add an "Import Settings" button when configuring campaign integrations, allowing users to copy settings from an existing campaign. Plus a quick link to Lead Type settings for field creation.

Benefits: Easy to build. Reduces work for new campaigns.

Limitations: Doesn't help existing campaigns. Still requires visiting each campaign individually. No standardised field names. Doesn't solve the field discovery problem.

**Solution C: Bulk Campaign Updates**
Add bulk operations to the Campaign Management screen — select multiple campaigns, choose "Copy Settings" from a source campaign.

Benefits: Updates hundreds of campaigns at once.

Limitations: Still requires initial configuration somewhere. Doesn't address field discovery. Risk of overwriting existing configurations.

### Decision: Solution A

I wrote the strategic recommendation. Key arguments:

1. **Customer value:** Directly eliminates the biggest source of pain (repetitive mapping). No competitor offers this.
2. **Architectural correctness:** Mappings are inherently tied to field definitions, which are defined at the Lead Type level. Housing mappings there is the logical, stable design.
3. **Protects existing work:** The lead engineer had nearly completed several integrations (DNC, IPQS, Trestle) built on the current campaign-level model. A Universal Campaign approach would force rewrites. Solution A kept his work valid.
4. **Competitive positioning:** Competitors reduce campaign sprawl through structural efficiency, but none solve mapping redundancy. Solution A leapfrogs rather than catches up.

---

## The Universal Campaign Debate (Aug 19, 2024)

### The CEO's counter-proposal
When our designer presented the Solution A mock to the CEO, the company president, the CMO, and me, the CEO raised a competitor-driven alternative: a Universal Campaign model where one campaign could serve multiple sources and buyers.

CEO's argument: competitors like LeadProsper structure their systems this way. Aligning with that pattern would make it easier for migrating users and reduce the total number of campaigns clients need to manage.

### The debate
This was a genuine strategic fork:

- **Designer (Lead Type approach):** Architecturally correct. Eliminates redundancy. Clean long-term solution.
- **CEO (Universal Campaign approach):** Closer to competitors. Potentially easier technically. Improves user flow without a major architectural rewrite.

### My analysis (post-meeting)
I wrote a detailed recommendation for Solution A as the end-state, structured around four arguments:

1. **Customer value:** Solution A saves more time and reduces error risk at the root cause — a bigger improvement than campaign centralisation alone.
2. **Architectural alignment:** Mappings belong to Lead Types, not campaigns. This makes reporting more consistent and prepares the system for faster integration with future validation tools, CRMs, and dialers.
3. **Risk of rework:** Universal Campaign would invalidate the lead engineer's near-complete integration work, forcing rewrites and delaying delivery.
4. **Competitive positioning:** Opportunity to differentiate rather than simply match competitors.

### Resolution
The team agreed to bring the lead engineer into the next session to evaluate technical feasibility. After his review, the team confirmed Solution A as the direction. The Universal Campaign concept was preserved as a potential future enhancement, not discarded — but the immediate path forward was Lead Type inheritance.

---

## Inheritance Model Details

### Criteria inheritance design
The team debated how campaign-level customisation should work. Key decisions:

- **Disable and add, not edit:** Campaigns cannot edit inherited criteria. They can disable a specific inherited rule and add a campaign-specific one. This keeps the Lead Type template as a clean source of truth.
- **Strongest outcome wins:** If both a Lead Type criterion and a campaign criterion evaluate for the same lead, the engine picks the more severe result (Reject beats QC).
- **Simple properties:** Campaign can override a scalar value or use the Lead Type default (toggle). UI shows inherited value as a greyed placeholder.
- **Response field mapping:** Kept at Lead Type level. Campaigns generally don't remap. Reporting uses metadata logs for aggregate queries; fields are only mapped to leads when needed in lead grids, downstream deliveries, or client criteria.

### Page revamp delivery
Structured in phases to avoid blocking the merge:
- **Critical path:** Showstoppers only. Fix critical flows, merge the branch, keep feature flag off if needed.
- **Post-merge polish:** UX polish, copy improvements, API key management enhancements.
- **Capability additions:** New capabilities (bulk enablement, advanced panels).

This kept the integration work shipping while the page architecture evolved underneath it.

---

## Individual Integration Delivery Pattern

Each of the six compliance integrations followed the same structure:

1. **Discovery:** API documentation review, endpoint analysis, field mapping, response handling, authentication method, rate limits, error scenarios
2. **Pitch:** Problem framing, solution hypothesis, scope (in/out), timeline, rollout plan
3. **Rollout:** Feature flag strategy, test scenarios, QA validation
4. **CS Prep Guide:** Step-by-step configuration instructions for Customer Success, troubleshooting section, FAQ
5. **Pricing:** Per-call cost analysis, client-facing pricing strategy, margin targets

This standardised approach meant each new integration could be scoped, built, and shipped faster than the previous one. The pattern itself became a reusable asset.

---

## Next Steps

To see how these discoveries informed the architectural proposal, see [03-pitch.md](./03-pitch.md). For details on how we executed this architecture, see [04-delivery.md](./04-delivery.md).
