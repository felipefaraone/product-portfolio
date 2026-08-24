# Delivery: Key Execution Decisions

This document covers the decisions made during execution that kept the program on track and prevented critical outages across 335,634 subscriber migrations.

---

## Decision 1: Technology as the Differentiator, Not Content

**The call:** Redesign the tier structure around platform capabilities instead of content access.

**Why it mattered:** The legacy model differentiated plans by content (Patriota gets documentaries, Núcleo de Formação gets education content, etc.). This meant:
- No natural upgrade path (why would a user move between content categories?)
- New features couldn't be gated by tier (if downloads become a feature, you can't monetize it if the tier structure is content-based)
- Operations burden: every content change required shipping across 15+ plan variants

**The solution:** Good (single screen, standard resolution), Better (multi-screen, offline downloads), Best (all features, highest resolution). Same model Netflix and Spotify use.

**The tradeoff:** Content-based differentiation was familiar to the existing subscriber base. Changing the model required reframing the value proposition in all marketing and onboarding materials. This made communication strategy critical — we couldn't ship the product and assume users would understand the value shift.

---

## Decision 2: Incremental Rollout by Renewal Batch, Not Big Bang

**The call:** Migrate subscribers in waves tied to their natural renewal dates, not all at once.

**Why it mattered:** A big bang migration of 335,634 subscribers creates:
- Single point of failure: if the algorithm has a bug, it affects everyone immediately
- No monitoring window: you can't catch issues before they become widespread
- Operational pressure: one-time cutover requires all systems to be perfect on day one

**The solution:** Group subscribers by renewal date. Run batches starting with the smallest cohort, monitor impact on billing system, access system, and member experience, then proceed to the next batch. This gave us:
- Early detection of edge cases (bugs surfaced in batch 1 of 10,000 members, not across 335,634)
- Time to validate: after each batch, we could audit the migration results before proceeding
- Sustainable operations: month-long rollout instead of a single dangerous night

**The execution:** This decision alone kept critical outages at zero. When bugs were found (and they were — edge cases with duplicate subscriptions, founders with special billing, gift cards with no activation status), they were found early, fixed in code, validated with synthetic tests, and then rolled forward to the next batch.

**The tradeoff:** The migration took months instead of days. This required sustained operational attention and communication discipline — we had to keep members informed that migration was ongoing, not assume they'd see a single notice and forget about it.

---

## Decision 3: Migration Algorithm Spec Before Engineering Started

**The call:** Before a single line of migration code was written, document the complete algorithm covering all edge cases.

**Why it mattered:** Migrating 335,634 subscribers requires handling:
- 15+ legacy subscription types
- 12,000 founders, 3,230 with additional paid plans generating R$2.6M (~A$730K at 2022 rates) in protected ARR
- ~5,000+ duplicate subscriptions (members who accidentally ended up with multiple active plans)
- ~9,000 unactivated gift cards
- Members with manual subscriptions alongside paid subscriptions
- Subscribers who originated from Hotmart, requiring separate resolution

If any of these cases were discovered mid-migration, the options were:
1. Manually fix them in production (error-prone, not auditable)
2. Halt the migration, fix the code, re-run (delays timeline, requires repeat communication)
3. Leave them unhandled (billing errors on high-value accounts)

**The solution:** I authored a detailed algorithm spec covering every edge case before engineering started:

```
For each subscriber:
  1. Get all active subscriptions (any type except "donator")
  2. Apply mapping rules to determine GBB equivalent
  3. Select subscription to keep:
     - Latest "paid" subscription if it exists
     - Otherwise, most recent of any type
  4. Cancel all other subscriptions
  5. Update plan on selected subscription (ID preserved, status updated)
```

Every edge case had an explicit resolution:
- **Duplicate subscriptions:** Keep the most recent paid subscription; cancel others
- **Founders with additional paid plans:** Map to Good tier; protect the R$2.6M (~A$730K at 2022 rates) ARR with clear communication
- **Mecenas (premium donors):** Maintain in local database; don't touch Guru billing
- **Unactivated gift cards:** CS action or refund — not part of migration
- **Hotmart subscribers:** Separate resolution track; don't include in primary migration
- **Manual/promotional subscriptions:** Include in mapping algorithm; select the latest paid one

**The validation:** Before any production run:
1. Generated preview outputs with and without founders segment
2. Ran against synthetic test users covering all edge cases
3. Ran against real members with upcoming renewals (smallest cohort first)
4. Audited migration results before proceeding to the next batch

**The execution:** This specification prevented the discovery of unhandled scenarios mid-migration. When the first batch ran, we knew what we'd see because we'd already specified how to handle it.

---

## Decision 4: Prerequisites as Non-Negotiable Scope

**The call:** Subscription Self-Management, Media Downloads, and Mobile Player Rebuild were not side projects — they were commercial prerequisites.

**Why it mattered:** The new tier structure only works if members can actually use the features that differentiate tiers:

- **Better tier includes offline downloads.** Without Media Downloads shipped, Better members pay for a feature that doesn't exist. This breaks trust and creates support burden.
- **Better tier includes multi-screen.** Without the Mobile Player Rebuild (which required DRM support for licensed content), multi-screen viewing isn't possible. Again, paid feature doesn't exist.
- **All tiers need self-service cancel.** Without Subscription Self-Management, members who don't like their mapped tier can't cancel themselves. This creates support backlog and worse churn outcomes.

**The sequencing:** I identified these dependencies upfront and made the call that they had to ship alongside the tier migration:

1. Mobile Player Rebuild (DRM support) — prerequisite for multi-screen, required for Better/Best to function
2. Media Downloads — prerequisite for offline playback, required for Better/Best to function
3. Subscription Self-Management — prerequisite for exit flow, required for member control
4. New subscription infrastructure (Mundipagg → Guru) — prerequisite for billing
5. Migration logic (batch rollout) — runs after all prerequisites are live

**The result:** When the migration launched, the Better and Best tiers had the actual product capabilities to support them. This is a portfolio-level PM call — recognizing that what looks like 4 separate cycles actually form a dependency chain, and sequencing them so they land together.

**The tradeoff:** This extended the program timeline. We couldn't migrate subscribers on day 1 if the product wasn't ready. But the alternative — shipping the migration without the features, then scrambling to ship downloads/multi-screen afterward — would have been worse for members and the business.

---

## The Dependency Graph

```
Mobile Player (DRM)  ─┐
Media Downloads      ─┼─→  GBB Tier Migration Launch
Self-Management      ─┤
Subscription Infra   ─┘
```

All four streams ran in parallel across 3 cycles. But they didn't land randomly. They landed in order, with the migration happening only after all prerequisites were live. This sequencing was the hardest part of the PM job.

---

## Program Outcome

- **~335,634 subscribers migrated** with zero billing disruptions and zero critical outages
- All 15+ legacy plan SKUs retired
- Plan distribution: Good 251,392 · Better 20,205 · Best 64,037
- Sustained operational discipline across 5 months and 4 concurrent squads

What made this possible: Clear decision-making on the hard tradeoffs (technology vs. content, incremental vs. big bang, spec before code, prerequisites before launch). Each decision reduced risk and shaped what came next.

---

See [04-results.md](./04-results.md) for the measured outcomes and lessons learned.
