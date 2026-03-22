# UI/Performance Issues — Discovery

**Contributors:** The CS team, Product, the project manager, the CEO, the designer
**Status:** Discovery complete — Engineering delivered the initial cycle

---

## 1. Purpose

This document summarises all evidence related to LeadExec UI slowness gathered by Product and CS using non-developer tools only.

The goal is to give engineering a consolidated, data-based starting point so developers can: validate the hotspots identified, decide what to investigate first, determine whether additional evidence is needed, and guide Product/CS on next steps.

This is not a technical root-cause diagnosis. It presents correlation only — no technical causality is implied.

---

## 2. Evidence Sources

### A. Clarity Data (Last 90 Days)

Three datasets extracted from Microsoft Clarity:
- **Top visited URLs** — where users spend the most time
- **URLs with lowest performance scores** — where rendering degrades most
- **Pages with highest JS error frequency** — where the UI breaks most often

### B. CS and Product Observations

Consistent reports across the team:
- Slow page loads on high-traffic screens
- Filters causing freezes, particularly on grid-heavy pages
- Inconsistent behaviour across sessions
- Screens getting progressively slower throughout the day

### C. Live Demo Observations

During demos at peak hours:
- Toolbar lag and disappearing icons
- Broken spinner (system-wide)
- Screens flashing incorrect content during transitions
- Load time variability reaching 20-30 seconds

---

## 3. What the Data Shows

### High-Traffic Pages (Clarity — Top Visited)

Pages with highest user exposure: Lead Grid (All Leads), Distribution, Client Details, Delivery Accounts, Delivery Methods, Lead Source List, Returns, Standard Reports, Pivot Report, Client Portal pages.

These align with CS and demo-reported slowness.

### Slowest Rendering Pages (Clarity — Lowest Performance Scores)

Pages appearing in the lowest performance tier: Lead Grid, Distribution, Pivot Report (high variance), Client Portal order screens, Delivery Accounts and Methods, Lead Source List, Returns.

### Pages With Most JS Errors (Clarity — Error Logs)

Error clusters appear around: toolbar navigation events, grid-heavy screens (DevExtreme), Client Portal transitions, and filtering-heavy actions.

These correlate with UI glitches observed during demos: spinner issues, intermittent blank states, flashing intermediate screens, toolbar icons disappearing.

---

## 4. Combined Evidence: Likely Hotspots

Based on overlap between high traffic, poor performance, and JS errors:

**Core System**
1. Lead Grid (All Leads)
2. Distribution
3. Client Details
4. Delivery Accounts
5. Delivery Methods
6. Lead Source List
7. Returns

**Reports**
8. Pivot Report
9. Standard Reports (under certain conditions)

**Client-Facing**
10. Client Portal (order creation and details)

**Global UI Infrastructure**
11. Left toolbar (lag, jank, disappearing icons)
12. Page transitions showing incorrect intermediate content
13. Broken spinner system-wide

---

## 5. Technical Context (Support Team Summary)

- Current LeadExec UI is built on an old jQuery-based architecture, which naturally contributes to sluggishness
- Many of the slowest screens use the DevExtreme Data Grid (jQuery version), heavy and slow when rendering large datasets
- DevExtreme has an Angular version, so migrating grids may not require full rebuilds
- Some screens may require improvements in data-loading logic
- Pivot Report speed varies depending on data volume
- Some Client Portal issues are CSS-related and already being addressed

---

## 6. Engineering Estimates

The engineer provided detailed line-by-line estimates for Angular migration of the major hotspots:

**Lead List Page (58 hours)**
- Header bar: 14 hours
- Filter bar: 12 hours
- DevExtreme grid (headers, columns, filters, lead fields, context menu, footer, totals, filter builder): 32 hours

**Distribution Page (62 hours)**
- Header bar: 14 hours
- Filter bar: 12 hours
- DevExtreme grid: 36 hours

**Client Details Page (78 hours)**
- Header bar: 4 hours
- General Information Panel: 6 hours
- Delivery Setting Tab: 6 hours
- Contact Tab: 6 hours
- Client Portal Tab: 10 hours
- Other Information Tab: 6 hours
- Orders Tab: 12 hours
- Delivery Account Tab: 8 hours
- Delivery Method Tab: 8 hours
- Distribution Assignments Tab: 12 hours

**Shared Components — Slideouts (64 hours)**
- Slideout Service and Container: 16 hours
- Quick View (Lead information): 24 hours
- Edit Order: 24 hours

**Shared Components — Modals (155+ hours)**
- 19 individual modals ranging from 3 to 24 hours each
- Largest: Add Lead (24 hours), Edit Delivery Account (30 hours), Edit Delivery Method (32 hours)

---

## 7. Product Interpretation

### Value assessment approach

Value was inferred from four signals:
1. **User exposure** — how often users are on the affected screens
2. **Perceived impact** — trust, stability, daily productivity
3. **Consistency of evidence** — analytics + CS + demos
4. **Risk of scope expansion** — how likely the fix bleeds into adjacent systems

This creates prioritisation driven by user pain and visibility, not by page count or engineering effort alone.

### Recommended sequencing

**Highest priority:** Lead Grid and Distribution. Highest traffic, worst performance, most JS errors, most cited in CS reports and demos. These are the pages where every customer spends the most time and where performance failures are most visible.

**High priority:** Client Details (complex page, many tabs, direct client interaction), shared slideout components (used across Lead List and Distribution).

**Medium priority:** Reports (Pivot Report, Standard Reports), Client Portal.

**Infrastructure:** Toolbar, page transitions, spinner — these affect the global experience and may have outsized perceptual impact relative to their fix effort.

---

## 8. Question for Engineering

Before continuing, we need engineering's guidance:

**A. Is the current evidence sufficient to begin technical review?**
If yes, we can move directly into analysis.

**B. If more evidence is needed, what specifically should Product/CS collect?**
Examples: a specific screen to record, a filter/path to reproduce, timestamps, console messages, session IDs, HAR files.

We will only collect what engineering explicitly requests.
