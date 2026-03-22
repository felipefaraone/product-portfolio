# Media Downloads 1.0 — Post-Mortem

## Delivery vs. Promise

| Deliverable | Status | Notes |
|------------|--------|-------|
| REST license endpoint (Hermes) | ❌ Not shipped | Deferred |
| Native offline playback (iOS + Android) | ⚠️ Partial | Both platforms shipped, but with significant technical debt |
| Flutter interface for BetterPlayer offline | ✅ Shipped | |
| Download management UI | ⚠️ Partial | Shipped but not end-to-end tested |
| Argos microservice | ✅ Shipped | |
| Admin panel for distributor rules | ❌ Not shipped | Rules require a developer to configure |
| Media pipeline backfill | ➡️ Not needed | Dependency shifted |

---

## What Went Wrong

### Planning

- **Underestimated complexity:** The project had multiple cross-stack dependencies (DRM, pipeline, microservice, mobile) and we didn't map their interdependencies explicitly at planning time.
- **No partial delivery plan:** Deliverables were framed as an all-or-nothing set instead of ordered by value — meant that when things slipped, there was no triage framework.
- **POC was inside the shape-up timeline:** Proof-of-concept work for BetterPlayer should have preceded the shape-up, not consumed part of it.
- **Rushed pitch:** The pitch was produced under time pressure, leading to low-resolution assumptions and higher downstream risk.
- **No QA plan:** Testing and integration were not scoped in the planning phase. Android shipped without end-to-end testing.
- **Didn't estimate developer effort for tasks they didn't own:** Some estimates reflected how long *we* thought it would take, not how long the assigned dev needed.

### Execution

- **Context switching:** Team members handled multiple concurrent responsibilities, reducing focus and throughput.
- **Late scope cuts:** We didn't cut scope as soon as it became clear we were over time — the Shape Up methodology calls for early cuts.
- **Microservice rework:** Implementation choices were revised mid-cycle, creating rework.
- **Communication gaps:** Engineers working through obstacles in isolation rather than escalating early. This was a structural failure: problems should have surfaced immediately, not been absorbed silently.
- **Misaligned fallback strategy:** The BetterPlayer fallback sequence was prioritized in a way that left less time for the primary player work.

### Structural / Organizational

- **Product not integrated throughout:** PM involvement dropped during execution — critical decisions were being made by engineers without business context.
- **Risk not revisited:** No structured weekly risk review; new rabbit holes were discovered but not escalated or replanned.
- **No shared source of truth for testing:** QA environment wasn't set up consistently, wasting debugging time.
- **Downloaded content was a GBB key differentiator** but its cross-squad dependencies (new player, pipeline, subscriptions) weren't given priority weight in the overall roadmap.
- **Team was using personal time** to meet project commitments — a signal of planning failure, not work ethic.

---

## What Went Right

- The Download Manager (Argos) microservice was well-designed and delivered.
- Offline playback (BetterPlayer + Flutter interface) shipped for both iOS and Android — core capability exists even if rough edges remain.
- Post-mortem itself was run with high candor — team surfaced systemic issues, not just tactical mistakes.

---

## Lessons

| Area | Learning |
|------|---------|
| Shaping | Spend more time eliminating rabbit holes before committing. Prototypes and POCs should precede cycles, not sit inside them. |
| Risk management | Revisit risks weekly, not just at kickoff. When new rabbit holes are found, replan — don't absorb them silently. |
| Scope | Cut scope earlier. Partial delivery on high-value items beats full delivery on lower-value ones. |
| PM integration | Product managers should be in the room for key technical decisions during execution, not just at planning and review. |
| Dependencies | Map all cross-squad dependencies at pitch time and build them explicitly into the appetite/timeline. |
| Communication | Culture change needed: engineers should escalate obstacles immediately, not soldier through them alone. This is structural — create safe channels for escalation and act on them fast. |
