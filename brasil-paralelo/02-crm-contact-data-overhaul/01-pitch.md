# CRM and Contact Data Overhaul — Pitch

## Problem

BP used three separate systems for member outreach: **Firebase** (push notifications), **ActiveCampaign / SendGrid** (email), and **Take Blip** (WhatsApp). Each system was operated independently by different business units with no coordination layer.

The results were predictable:
- Members received overlapping messages across channels (email + WhatsApp + push on the same day)
- No frequency controls — nothing prevented the same member from receiving three commercial messages in a week
- No unified view of campaign history per member
- Third-party tools (ActiveCampaign, Take Blip) had direct access to member contact data, creating security and compliance exposure

**Member feedback examples:**

![Member complaint — receiving campaign email to re-register for a documentary](images/member-complaint-wrong-email.png)
*(Member received a "register to watch new documentary" email despite already being a paying member.)*

![Member complaint — duplicate communication](images/member-complaint-duplicate-comms.png)

**Direct quotes from CS and CRM leads during discovery interviews:**
- "We need integrated data from other tools to make decisions"
- "We can't prevent members from being exposed to repetitive campaigns"
- "Lead generation is unreliable — we can't trust the lists"
- "We can't be proactive about member experience because we don't have a single view"

### Core problem (distilled)

Member communications were excessive and uncoordinated, and there was no tooling to enforce frequency governance across channels.

---

## Appetite

**Big Batch — 6 weeks**

| Role | Allocation |
|------|-----------|
| 1 data engineer | Full-time (integrations + streaming) |
| 1 backend engineer | Full-time |
| 1 frontend engineer | Full-time |
| 1 data analyst | Full-time (data model + validation) |

---

## Solution

An internal campaign orchestration platform with four core capabilities:

### 1. Contact Data Centralization
- Import 1.5M+ contact records from ActiveCampaign into BigQuery
- Normalize against BP member database (subscriber status, plan, activity)

### 2. Lead Generation Engine
- Generate leads by combining contact records with platform behavioral events (e.g., `upsell_viewed`, `media_reproduced`, `login`, `start_session`)
- Supports rule-based segmentation (e.g., "members who haven't watched in 14 days and are on Good tier")

### 3. Campaign Builder
- Define campaigns with type (commercial, engagement, informational), target segment, and channel
- Campaigns send lists to existing tools (SendGrid, Take Blip, Firebase) via API — no replacement of send infrastructure
- Schedule recurring campaigns with event-based triggers

### 4. Frequency Controls (Freeze Rules)
- Per campaign type, define:
  - **Freeze window:** days after a campaign before the same member can receive another of the same type
  - **Minimum interval:** global cooldown between any outbound communications
- Freeze state stored and enforced before any send

**Data persistence:**
All campaigns and participant lists are saved to BigQuery for downstream analytics.

---

## No-Goes

- Building new send infrastructure for push, email, or WhatsApp — existing tools are kept
- Campaign performance dashboards (deferred to next cycle)
- Real-time streaming processing (batch approach in v1)

---

## Rabbit Holes

- **ActiveCampaign data quality:** 1.5M+ contact records — but an unknown percentage were duplicates, outdated, or not matched to a current BP member. Deduplication and normalization against the BP member database was a non-trivial data engineering task, not just a migration.
- **Streaming vs. batch:** Real-time event processing via Kafka was evaluated but deferred. Email and WhatsApp campaigns don't require sub-second latency. BigQuery batch jobs (hourly / daily) were sufficient for v1 and dramatically reduced operational complexity.
- **Freeze rule edge cases:** Defining frequency rules sounds simple until you ask: what happens if a member is in two different campaigns of the same type that fire on the same day? Which one wins? Rules needed a precedence model before enforcement logic could be built.
- **Frontend scope:** The campaign management admin panel was a significant standalone build — it was the largest single frontend effort in the project and carried the most uncertainty at pitch time.
- **Third-party API reliability:** Sending lists to SendGrid and Take Blip via API meant the platform's reliability was partially dependent on external API uptime. Retry logic and failure alerting were part of the engineering scope, not just nice-to-haves.

---

## Success Metrics

| Metric | Definition |
|--------|-----------|
| Communication overlap rate | % of members receiving >1 campaign message per day — target: ↓ from baseline |
| Lead list accuracy | % of generated leads matching actual member status — target: >95% |
| Campaign send latency | Time from campaign trigger to list delivery to send platform — target: <4 hours for batch |
| Freeze rule coverage | 100% of campaigns respect frequency rules before sending |
| CS escalations about over-communication | Target: ↓ measurable reduction within 60 days of launch |

---

## What Shipped

- [x] 1.5M contact records from ActiveCampaign in BigQuery
- [x] Lead generation engine with event-based segmentation
- [x] Campaign builder with frequency freeze rules
- [x] API integrations to deliver lists to SendGrid, Take Blip, Firebase
- [x] Campaign and participant history stored in BigQuery
- [ ] Performance dashboards (deferred)
- [ ] Take Blip direct integration (deferred — lower priority given SendGrid coverage of same use cases)

---

## Dependencies

| Dependency | Owner | Notes |
|-----------|-------|-------|
| ActiveCampaign data export | Marketing | One-time migration |
| Firebase event data in BigQuery | Data Engineering | Already partially available |
| SendGrid API access | DevOps / Security | Third-party credentials management |
| Take Blip API access | CRM team | Deferred in v1 |

---

## Outcomes

**Measurement window: 60 days post-launch (May – June 2022)**

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Avg. sends per member per week (all channels) | ~4.1 | ~1.9 | **−54%** |
| Lead list accuracy (vs. actual member DB) | ~58% | ~96% | **+38pp** |
| CS escalations about communication fatigue | Baseline | **−67%** | Qualitative → quantified |
| Campaign delivery time (manual → automated) | ~3 days | ~4 hours | **−95%** |

**First campaign through the platform:**
- Segment: members with no platform activity in the last 21 days, on the Good tier
- Channel: email (SendGrid) + WhatsApp (Take Blip)
- Reach: ~52,000 members
- Re-engagement rate: **13.4%** vs. ~6.8% historical baseline from ActiveCampaign
- The improvement is attributed primarily to list accuracy — previously, campaigns sent to ~58% accurate lists meant ~42% of sends were wasted on churned or inactive contacts, depressing response rates and damaging deliverability scores

**Structural outcome:** The data layer built here — 1.5M contacts normalized against the member DB with behavioral event enrichment — became the foundation for the Lead Scoring model that was prioritized in the following quarter. The campaign platform was the first time the company had a single, trustworthy member communications dataset.
