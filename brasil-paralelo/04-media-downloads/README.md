# Media Downloads 1.0 — Offline Playback for Subscription Differentiation

**Brasil Paralelo · 2022 · Shape Up Big Batch (6 weeks)**

---

## Problem

Offline playback was a hard requirement for the Better and Best tiers of the upcoming GBB subscription model. Without it, there was no functional differentiator justifying the higher price point — the tiers would be selling a promise, not a capability. This made Downloads a commercial blocker for the entire GBB program.

The project spanned 5 technical layers (DRM license server, microservice, admin panel, mobile client, media pipeline) with significant cross-squad dependencies.

---

## My Role

Sole PM. Led discovery, priority escalation, delivery, and the post-mortem that changed the team's process for subsequent Big Batch projects.

---

## Key Decisions

**1. Elevated from feature request to commercial blocker**

Downloads had been deprioritised as a "nice-to-have" feature request. I reframed it as a commercial blocker for GBB: without offline playback, the Better and Best tiers had no technology-based differentiator. This changed its priority and resource allocation entirely.

**2. Defined event schema and KPI framework from zero baseline**

Offline playback had no existing metrics — no prior feature to benchmark against. I defined the event schema (download initiated, completed, played offline, deleted) and KPI framework before development started, ensuring we could measure adoption from day one.

**3. Mid-cycle descoping to protect the GBB launch date**

When it became clear that the Hermes REST endpoint would not be ready in time, I made the call to descope it from the current cycle. This protected the GBB launch date — the core download capability shipped on schedule while the API endpoint moved to a subsequent cycle.

**4. Post-mortem with full candour**

The project surfaced significant planning failures around cross-squad dependency management and estimation accuracy. I led the post-mortem without softening the findings. The practices introduced (explicit dependency mapping, pre-cycle risk assessment) became standard for all subsequent Big Batch projects at BP.

---

## Results

- GBB tier **launched on schedule** — Downloads was the critical-path dependency
- **~22%** of eligible members downloaded at least one title within 30 days
- Average **4.7 downloads per user**
- Post-mortem practices adopted as standard across the engineering organisation

---

## Documentation

| Document | What it covers |
|----------|---------------|
| [01-pitch.md](./01-pitch.md) | Full Shape Up pitch: problem framing, appetite, solution architecture, rabbit holes, no-goes, and measured outcomes |
| [02-post-mortem.md](./02-post-mortem.md) | Post-mortem covering delivery vs. promise, planning failures, execution gaps, lessons learned, and process improvements adopted organization-wide |
