# Delivery — How We Shipped the Integrations Ecosystem

## Overview

This document covers the execution story: how we shipped six compliance integrations in parallel with a complete platform redesign, resolved the Lead Type inheritance decision, and managed the tension between speed and architecture.

The program spanned ~12 months (Jan–Dec 2024) and involved coordinating multiple workstreams: individual integration development, page redesign architecture, Lead Type inheritance implementation, and enterprise customer unblocking (AHS).

---

## Strategic Decisions

### 1. Lead Type Inheritance Model Over Universal Campaign

**The decision:** After the Aug 19, 2024 debate with the CEO on the competitor-driven Universal Campaign approach, we committed to the Lead Type inheritance model as the architectural direction.

**Why it mattered:** This was the single highest-impact decision of the program. The alternative (Universal Campaign) would have required the lead engineer to rewrite integrations already in flight.

**The tradeoff:** Higher development complexity and the need to learn unfamiliar parts of the Lead Type architecture, versus the benefit of an unsolved capability that competitors didn't have and the protection of existing work in progress.

**Execution:** We brought the lead engineer into a technical feasibility session. His confirmation that the Lead Type approach was architecturally sound gave the team confidence to move forward.

---

### 2. Centralised Criteria Management at Campaign Level

**The decision:** Criteria (both for field-level filters and integration responses) would be managed at the campaign level in a unified criteria builder, not scattered across individual integrations.

**Why it mattered:** This kept the configuration mental model simple and supported complex multi-condition logic that enterprise customers needed (e.g., "if fraud score below 75, reject; if 75-85, send to QC; if above 85, reject").

**The tradeoff:** More engineering complexity to build a unified criteria engine, versus the benefit of flexible, customer-facing control.

---

### 3. Post-Phase Validation Only, Not Ping

**The decision:** Compliance validations would run during Post (lead acceptance), not Ping (auction response).

**Why it mattered:**
- Kept Ping responses fast, which is critical in real-time auction environments where milliseconds matter
- Enabled cost optimisation: once a lead failed one validation and "skip remaining" was enabled, we didn't burn API credits on subsequent checks
- Gave clients a real choice between speed (parallel integrations) and cost (sequential)

**The tradeoff:** Some clients in auction environments might have preferred pre-Ping validation to avoid accepting bad leads. We documented this decision and made it clear to the project manager and Customer Success.

---

### 4. Ship Integrations in Parallel with Page Redesign, Not Sequentially

**The decision:** Rather than wait for the page redesign to be complete before shipping integrations, the lead engineer built and released integrations behind feature flags using the existing (imperfect) UI, while the page revamp progressed in parallel.

**Why it mattered:** Enterprise clients (especially AHS) got compliance capabilities months earlier than they would have under a sequential approach. This kept the relationship with AHS healthy and prevented potential churn.

**The tradeoff:** Some early integrations launched with UI that was later replaced. This required some rework of UI flows during the merge.

**Execution:** Feature flags allowed us to ship integrations to specific customers (like AHS) without exposing incomplete UI to the broader user base.

---

## Workstream Structure

### Workstream 1: Individual Compliance Integrations (Lead Engineer + Development Team)

**Timeline:** Jan–Dec 2024

**Pattern:** Each integration followed an identical cycle:
1. API discovery and requirements analysis
2. Pitch and stakeholder validation
3. Build and QA
4. CS prep guide and pricing strategy
5. Feature flag rollout

**Integrations shipped:**
- PureCallerID (Jan, AHS enterprise requirement)
- Trestle (Mar)
- DNC.com (Jun)
- IPQS (Aug)
- TrustedForm (Sep)
- Abstract API (Nov)

**Key insight:** By standardising the delivery process, each successive integration shipped faster than the previous. We learned what questions to ask, how to structure API discovery, and how to prepare Customer Success. By integration #5, we had a scalable machine.

**Stakeholder context:** Each integration had its own business driver:
- PureCallerID: AHS compliance requirements
- DNC.com: TCPA litigation risk mitigation
- IPQS: Fraud scoring capability gap
- Others: General compliance platform completeness

---

### Workstream 2: Integrations Page Redesign (Designer + Lead Engineer)

**Timeline:** May–Nov 2024 (parallel with integrations)

**What it replaced:** Static toggle list of integrations with no configuration depth

**What we built:**
- Integration cards with enable/disable toggles and API key management
- Per-Lead-Type configuration flow with request mapping, response field selection, and criteria builder
- Visual distinction between inherited defaults and campaign-level overrides
- Campaign routing surface to show integration configuration at the campaign level

**Phased delivery:** We broke the page work into phases to avoid blocking the entire program on any single piece:
- **Critical path (merge-blocking):** Critical flows only. Showstopper bugs. Merge the branch, feature flag remains off.
- **Post-merge polish:** UX polish, copy improvements, API key management enhancements
- **Capability additions:** Advanced panels, bulk enablement features

This prevented integration work from being invalidated by page architecture changes.

---

### Workstream 3: Lead Type Inheritance Model (Architect + Designer + Lead Engineer)

**Timeline:** Aug–Dec 2024

**Key decision cycles:**
- **Aug 19:** Universal Campaign debate with leadership (CEO, company president, CMO)
- **Aug 26:** Lead engineer technical feasibility review → confirmation of Solution A
- **Sep–Nov:** Design and implementation of inheritance patterns

**What we built:**
1. **Inheritance rules:** Lead Type settings apply to all campaigns. Campaigns can disable inherited rules and add campaign-specific ones.
2. **Criteria inheritance logic:** "Strongest outcome wins" — if both Lead Type and campaign criteria evaluate, the more severe result (Reject > QC > Pass) wins.
3. **Field mapping:** Kept at Lead Type level. Response fields auto-created at Lead Type level when integration enabled, standardising field names across all campaigns.
4. **Override UI:** Campaign-level criteria builder shows inherited rules greyed out, with clear UI for adding overrides.

**Tension:** Building a clean inheritance model required architectural work that designers and engineers had to learn as they built. There were moments where we debated whether the abstraction was worth the complexity. It was. Customers immediately understood the model because it matched their mental model: a Lead Type defines a class of leads, and integrations apply to all leads of that class.

---

## Enterprise Customer Unblocking (AHS)

**The situation:** AHS, a Fortune 500 insurance company, had compliance requirements that LeadExec couldn't meet. Specifically, they needed real-time phone validation and TCPA risk detection.

**How we unblocked them:**
1. **Jan 2024:** PureCallerID built and feature-flagged to AHS only
2. **Feb 2024:** AHS began using PureCallerID in production, validating phone numbers in real-time
3. **Jun–Aug 2024:** Added DNC.com and IPQS to their validation chain
4. **Sep 2024:** TrustedForm added for consent validation

**Impact:** By the end of 2024, AHS had a complete compliance stack. This prevented potential contract churn and enabled expansion conversations about additional validation requirements.

**Key learning:** Shipping early (even behind feature flags with imperfect UI) kept the relationship moving forward and de-risked the larger architecture work.

---

## Key Design Decisions

### Lead Type Inheritance: "Disable and Add, Not Edit"

**The decision:** Campaign-level customisation of inherited integration rules follows a specific pattern: you can disable an inherited rule and add a new campaign-specific rule, but you cannot edit an inherited rule directly.

**Why:** Keeps the Lead Type as the single source of truth. If multiple campaigns could edit the same inherited rule differently, the Lead Type would become ambiguous. A user looking at the Lead Type wouldn't know if a rule applied to all campaigns or just some.

**Tradeoff:** Slightly more UI complexity (two buttons instead of inline edit), but much clearer semantics and easier to trace which rule applies where.

---

### Response Field Mapping at Lead Type Level

**The decision:** When an integration is enabled at the system level, its response fields are auto-created as global fields. Users configure which fields to capture per Lead Type.

**Why:** Response fields are inherently tied to field definitions, which are defined at the Lead Type level. If each campaign could remap response fields differently, you'd have the same repetitive configuration problem we were trying to solve.

**Tradeoff:** Constrains flexibility (all campaigns using a Lead Type capture the same response fields from an integration), but solves the field discovery and standardisation problem. Campaigns that need different fields can create a new Lead Type or use campaign-level criteria to discard unwanted fields post-capture.

---

### Strongest Outcome Wins

**The decision:** When both a Lead Type criterion and a campaign criterion evaluate for the same lead, the engine applies the more severe result.

Example: Lead Type rule says "if fraud score above 85, reject." Campaign rule says "if fraud score above 90, reject." For a lead with fraud score 87, the Lead Type rule (Reject) wins.

**Why:** Prevents a scenario where a campaign override accidentally weakens compliance. Insurance and finance customers need to know that they can't accidentally weaken a critical control.

**Tradeoff:** Removes some flexibility, but the pattern itself is discoverable and matches customer expectations.

---

## Delivery Rhythm and Communication

### Cadence
- **Weekly syncs** with design, engineering, and PM to identify dependencies
- **Bi-weekly architecture reviews** with the lead engineer and architect to validate design decisions
- **Monthly stakeholder reviews** with leadership (CEO, project manager, company president) to review progress and surface competitive or customer context changes

### Managing Tension
The biggest tension was between **shipping fast** (getting integrations to AHS) and **building right** (creating a clean inheritance architecture that would scale).

Solution: **Feature flags.** This let us ship fast (early integrations to AHS behind flags) while building right (page redesign and inheritance model in parallel). When they merged, enterprise customers had months of early access, and we had a mature architecture.

---

## Lessons Learned

**1. Standardising delivery process is a force multiplier.** By integration #3, we had a reusable pattern (discovery → pitch → build → rollout → CS guide → pricing). This made integration #6 faster than integration #1.

**2. Ship early, iterate late.** Feature flags allowed us to get compliance capabilities to AHS months before the full redesign was complete. This kept relationships healthy and gave us real customer feedback to inform the architecture.

**3. Protect in-flight work.** The decision to rule out Universal Campaign was largely driven by protecting the lead engineer's near-complete integration work. This is a valid and important consideration in architectural decisions.

**4. Inheritance is hard to explain, easy to use.** Once AHS and other customers started using the Lead Type inheritance model, they understood it immediately. But explaining it in meetings was challenging. Mockups and live walkthroughs helped more than written specs.

---

## Next Steps

For the full story of outcomes and impact, see [05-results.md](./05-results.md). For the technical details of each integration, see [06-compliance-integrations.md](./06-compliance-integrations.md).
