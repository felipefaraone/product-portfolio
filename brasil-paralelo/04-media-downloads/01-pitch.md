# Media Downloads 1.0 — Pitch

## Problem

Brasil Paralelo was launching a new tiered subscription model (Good/Better/Best — GBB). The "Better" and "Best" tiers required offline media playback as a core differentiator. Without download support, the new tier couldn't go to market — this was a hard blocker on the commercialization strategy.

Additionally, content distributor contracts for licensed media imposed per-distributor download rules (maximum download counts per user, license expiry windows). This meant that access rules had to be enforced server-side and configurable by the content team — not just handled in the client.

---

## Appetite

**Big Batch — 4 weeks**

| Role | Allocation |
|------|-----------|
| 2 mobile engineers (Android + iOS) | Full-time |
| 1 backend engineer | Full-time |
| 1 data engineer | As needed |

This project spans five system layers simultaneously: admin panel, DRM license server, download microservice, mobile client, and media pipeline. The risk of cross-layer dependencies was the primary planning concern.

---

## Solution

A full-stack offline playback system designed around two constraints: plan-based access rules (which tier can download what) and distributor-based rules (contractual limits per content partner).

### Back-office (Caverna — Admin Panel)
- Content team registers a distributor with a maximum download limit per user
- Content team assigns media to a distributor
- Rules can be overridden per individual media title (within distributor limits)

### Download Microservice (Argos)
- Enforces download permissions against both plan-level rules and distributor-level rules
- Maintains a per-user, per-device record of active downloads
- Storage: XTDB (document store) with Postgres as the transaction log — first production use of XTDB at BP

### Client (iOS + Android)
- User can download, play offline, and delete media
- Offline DRM license stored securely (iOS Keychain / Android Keystore)
- Default license expiry: 30 days from download
- Download state syncs every time the user comes online (eventual consistency model)
- Events emitted for all actions: download requested, download completed, offline play, deletion, sync

### Hermes (DRM License Server)
- REST endpoint for offline license retrieval — replaces the proprietary interface used for streaming

### Media Pipeline
- New media processed with download-compatible tags at ingest
- Backfill of existing catalog (dependent on separate pipeline shape-up)

### System Architecture

```
download_manager ↔ plataforma2020   (user/plan verification)
download_manager ↔ argos             (download rules enforcement)
argos ↔ keyos/BuyDRM                 (DRM license issuance)
```

**Design reference:** [Figma — App Downloads UI](https://www.figma.com/proto/H1vs2zz3iOyqDDjpdzikcL/22Q2-App)

---

## No-Goes

- Offline cache for catalog metadata, banners, or search (media files only)
- Automatic license renewal on expiry
- Wi-Fi-only download restriction (user preference setting)
- REST endpoint migration for other Hermes clients (only the download use case)
- CS-facing audit interface for member download history

---

## Rabbit Holes

- **BuyDRM/KeyOS offline support:** Support for downloadable assets was assumed at the start of the project but required explicit validation with the vendor — confirmed early to avoid a late blocker.
- **XTDB in production:** First production use at BP. Limited internal knowledge base meant debugging and operational issues would be slower to resolve than with established datastores.
- **Pipeline dependency:** Full catalog backfill required the elastic media pipeline shape-up to ship first. External dependency with an independent timeline — backfill was explicitly not in this project's appetite.
- **BetterPlayer upstream:** Some open source PRs sit unapproved for weeks. Decision: maintain a BP fork to avoid being blocked on upstream velocity.
- **DevOps involvement:** Creating download-compatible media test assets required DevOps participation — not anticipated at pitch time.

---

## Success Metrics

| Metric | Definition |
|--------|-----------|
| Content rule self-service | Content team can create and edit distributor rules without engineering involvement |
| License delivery | Offline DRM licenses issued correctly across iOS and Android device matrix |
| Download enforcement | Concurrent download limits respected per plan and per distributor contract |
| Download auditability | Full per-account download history queryable in Argos |
| Event completeness | All defined events firing and landing correctly in the data lake |

### Product KPIs (Post-Launch Tracking)

| Metric | Segmentation |
|--------|--------------|
| Avg. downloads per active user | By plan tier (Better/Best), by content type |
| Most-downloaded titles + avg. % watched offline | By plan, by content type |
| Download-limit errors → plan upgrade correlation | Good tier vs. Better/Best |
| % of downloaded titles never played offline | By plan, by content type |

### Analytics Event Schema

```
download_requested   { media_id }
download_denied      { media_id, reason }
download_completed   { media_id }
download_removed     { media_id }
offline_sync         { media_id, checkpoint }   // on app open while online
```

---

## What Shipped

| Item | Status | Notes |
|------------|--------|-------|
| Native iOS + Android download and offline playback | ✅ Shipped | Technical debt acknowledged; needs follow-up cleanup |
| Flutter interface layer for BetterPlayer offline mode | ✅ Shipped | |
| Download management UI in the app | ✅ Shipped | Not end-to-end tested before release |
| Argos — download management microservice | ✅ Shipped | |
| Caverna — admin panel for distributor rules | ⚠️ Partial | Rules must be created by an engineer; UI not delivered |
| Hermes — REST offline license endpoint | ❌ Deferred | Descoped to protect core outcomes |
| Media pipeline backfill | ❌ Blocked | Dependent on separate pipeline cycle |

---

## Dependencies

| Dependency | Owner | Risk Level |
|-----------|-------|------------|
| Elastic media pipeline (for catalog backfill) | Content platform squad | High — external timeline |
| New subscription tier structure | Acquisition squad | High — plan permissions are core to enforcement logic |
| BuyDRM/KeyOS downloadable asset support | External vendor | Medium — needed early validation |
| DevOps media asset creation for QA | DevOps | Medium — unexpected involvement |

---

## Outcomes

**The primary outcome was enabling the GBB tier to launch on schedule.** Without offline playback, the Better and Best tiers couldn't go to market. The feature shipped in time for the commercial launch — that was the definition of success for this project.

**Measurement window: 30 days post-launch**

| Metric | Result |
|--------|--------|
| Better/Best members who downloaded at least 1 title | ~22% of eligible base |
| Average downloads per active downloader | ~4.7 titles/user |
| % of downloaded titles actually played offline | ~68% |
| Most common download-to-play gap | Under 48 hours (mostly commute and travel use cases) |

**Process outcome:** The post-mortem from this project was adopted as a template for all subsequent Big Batch projects at BP. The specific practices introduced — weekly risk review, explicit dependency mapping at pitch time, mandatory test plan before execution — became part of the standard shaping process.

This was a meaningful organizational outcome beyond the feature itself: the project's failures became a shared lesson rather than a scar.
