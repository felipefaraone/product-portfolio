# Results: Migration Outcomes and Lessons Learned

## Migration Metrics

**~335,634 subscribers migrated** — the entire active subscriber base — with zero critical outages and within the planned rollout timeline.

### Plan Distribution

| Tier | Subscribers | % of Base |
|------|------------|-----------|
| Good | 251,392 | 74.8% |
| Better | 20,205 | 6.0% |
| Best | 64,037 | 19.1% |
| **Total** | **335,634** | **100%** |

### Edge Cases Resolved

| Scenario | Population | Resolution | Outcome |
|----------|-----------|-----------|---------|
| Duplicate subscriptions | ~5,000+ | Kept most recent paid; cancelled others | All resolved ✅ |
| Founders with additional paid plans | ~3,230 | Mapped to Good; communicated separately | R$2.6M (~A$730K at 2022 rates) ARR protected ✅ |
| Mecenas (premium donors) | ~1,042 | Maintained locally; Guru untouched | Zero billing changes ✅ |
| Unactivated gift cards | ~9,000 | CS action or refund | Processed separately ✅ |
| Manual/promotional subscriptions | ~3,598 | Included in mapping algorithm | All accounted for ✅ |

## Business Impact

**Plan simplification:** Seven content subscriptions and 15+ legacy plan SKUs retired; acquisition now operates on a single, coherent tier structure.

**Revenue (measured):** Annual revenue doubled in the 12 months following the migration.

**Churn (measured):** Annual churn fell from 58% to 28% (roughly 7% to 3% monthly) over the same period, alongside the platform capabilities that shipped with it (downloads, multi-screen, self-management). Still trending down when I left.

**Revenue trajectory (projected):** Lifetime PBT of R$27,387,500 (~A$7.7M at 2022 rates) on a program cost of R$70,000 (~A$20K at 2022 rates) (391:1 ratio).

**Member experience:** Clear value ladder for the first time — Básico → Intermediário → Acesso Total 4K. Each tier upgrade has a tangible, demonstrable benefit.

**Operational efficiency:** Reduced plan variants from 15+ to 3. Fewer communication templates, fewer billing configurations, fewer edge cases to handle in downstream systems.

---

## Operational Execution

**Zero critical outages:** Incremental rollout by renewal batch prevented single points of failure. Bugs were caught early (batch 1 of 10,000 members, not across 335,634) and fixed before proceeding.

**Access impact:** Fewer than 0.5% of migrated members reported access complaints post-migration — within our target.

**Billing accuracy:** Zero billing disruptions. Every migrated subscriber's billing status, renewal date, and subscription ID were preserved accurately.

**Communication:** Two-track strategy — personalized migration emails for existing members 7 days before renewal, new checkout flow for new members (GBB only). No member confusion about which tier they were in.

---

## What Worked

**1. Decision clarity drives execution speed**

Each of the four key decisions (technology vs. content, incremental vs. big bang, spec before code, prerequisites before launch) answered a specific risk and shaped what came next. There was no ambiguity about why we were doing things this way — the team understood the tradeoffs and the reasoning.

**2. Incremental delivery prevents catastrophic failure**

Batch rollout by renewal date turned a 335k-member migration into 10 smaller problems with monitoring in between. This single decision kept critical outages at zero and gave us the ability to detect and fix bugs without affecting the entire subscriber base.

**3. Specification before implementation avoids mid-migration discovery**

Documenting the algorithm and all edge cases before engineering started prevented the scenario where we discover an unhandled case halfway through and have to halt the migration. Every case was known, resolved, and validated before code was written.

**4. Prerequisites as scope discipline**

Shipping downloads, player rebuild, and self-management alongside the migration meant members could actually use the features that justified the tier migration. This is harder than shipping the migration alone, but it's the right thing for the member experience and the business.

---

## What Would Be Done Differently

**1. Communication cadence**

In hindsight, we could have started communicating the tier change earlier — showing members what the new model would mean for them, letting them ask questions before migration. We did communicate, but starting it 3 months earlier instead of 1 month would have reduced post-migration support burden.

**2. Self-service tier selection at migration time**

We launched Subscription Self-Management 1.0 with basic cancel/pause functionality. A "choose your tier at migration time" flow would have let members who didn't want their mapped tier select something different immediately. Instead, those members went through support. This was deferred to post-migration and shipped later.

**3. Trial eligibility for migrated users**

We didn't re-enable trials for members who were on old plans and being migrated. Some members who had used a trial years ago expected to be eligible again when switching tiers. This generated support cases and should have been handled upfront with either a decision to allow re-trials or explicit communication that migrated members weren't eligible.

**4. Hotmart migration automation**

Hotmart-originated subscribers went through a separate resolution track because the billing data was external. This was handled but not automated. Automating this path would have reduced manual work and further simplified operations.

---

## What Was Learned

**Program management lesson:** At scale, the hard part of PM is not designing the product. It's dependency management and decision-making under uncertainty. This program had 4 concurrent work streams across 60+ engineers. Sequencing them correctly, making clear tradeoffs, and communicating why so the team understood what came next — that's what separated "on time with zero outages" from "crisis mode".

**Tier design lesson:** Technology as differentiator beats content as differentiator for subscription growth. Content changes constantly; technology capabilities are sticky. Netflix charges more for multi-screen because it's a meaningful feature, not because they had to create new content. We did the same.

**Rollout lesson:** Incremental delivery with monitoring between batches is the lowest-risk way to handle large-scale migrations. It's slower, but it's the difference between "zero outages" and "multiple critical incidents that require manual recovery".

**Migration lesson:** Edge cases aren't edge cases at scale. 5,000+ duplicate subscriptions, 3,230 founders with special billing, 9,000 unactivated gift cards. These aren't 1% problems. They're 2-3% of the base. You have to spec for them or you'll discover them mid-migration.

---

## Demo and Resources

- [GBB Subscription Model Launch Video (YouTube)](https://youtu.be/gEQh6SR9oHk)

---

See the complete program structure: [README.md](./README.md) links to problem framing, pitch, and execution narrative.
