# UI/Performance Issues — Pitch

## Problem

LeadExec's UI performance issues create friction in demos, onboarding, and daily operations. High-traffic pages load slowly, filters freeze, and the navigation toolbar behaves inconsistently — hurting first impressions and user confidence.

The symptoms are visible across every customer touchpoint:
- Lead Grid and Distribution pages experience inconsistent load times, sometimes reaching 20-30 seconds at peak hours
- Filters cause page freezes on grid-heavy screens
- The left toolbar shows lag, disappearing icons, and jank during navigation
- Page transitions flash incorrect intermediate content
- The spinner system is broken system-wide
- Screens get progressively slower throughout the day

These issues appear most frequently on pages built on the legacy jQuery architecture with DevExtreme DataGrid components.

## Jobs To Be Done

**When I navigate between LeadExec pages,** I want screens to load quickly and consistently, so I can trust the system and stay productive.

**When I apply filters or interact with grids,** I want the UI to respond without freezing or glitching, so I can work efficiently even with large datasets.

**When I demo LeadExec to prospects,** I want key screens to load smoothly, so I can create confidence instead of friction.

**When I troubleshoot client issues,** I want predictable performance on the most used pages, so I can resolve cases faster and reduce repeat tickets.

## Value Proposition

A faster, more stable UI directly improves first impressions, increases demo conversion, reduces CS workload, and enhances overall usability. Addressing key hotspots — Lead Grid, Distribution, Reports, Client Portal — creates immediate value for every customer and positions LeadExec as a modern, reliable enterprise platform.

## Approach

This initiative follows a discovery-first model. Product and CS gathered all available evidence using non-developer tools, consolidated it into a structured package, and handed it to engineering for technical diagnosis.

### Evidence sources
1. **Clarity analytics (90 days):** Top visited URLs, URLs with lowest performance scores (slowest rendering), pages with highest frequency of JavaScript errors
2. **CS and Product observations:** Consistent reports of slow page loads, filter freezes, inconsistent behaviour, screens getting progressively slower throughout the day
3. **Live demo observations:** Toolbar lag, broken spinner, screens flashing incorrect content during transitions, 20-30 second load time variability at peak hours

### Hotspot identification
Cross-referencing all three sources, 13 areas surface as the most likely problem zones:

**Core System:** Lead Grid (All Leads), Distribution, Client Details, Delivery Accounts, Delivery Methods, Lead Source List, Returns

**Reports:** Pivot Report, Standard Reports (under certain conditions)

**Client-Facing:** Client Portal (order creation and details)

**Global UI Infrastructure:** Left toolbar (lag, jank, disappearing icons), page transitions showing incorrect intermediate content, broken spinner system-wide

These are not conclusions — they are evidence-based hotspots that consistently appear across analytics, CS reports, and live observations.

## Engineering Scope

The engineer provided line-by-line estimates:
- Lead List Page: ~58 hours (header bar, filter bar, DevExtreme grid with all sub-components)
- Distribution Page: ~62 hours (same structure as Lead List with additional complexity)
- Client Details Page: ~78 hours (8 tabs plus header and panels)
- Shared Components — Slideouts: ~64 hours (slideout service, container, Quick View, Edit Order, Edit Payments, Edit Delivery Account, Edit Delivery Method)
- Shared Components — Modals: ~155 hours (19 individual modals)

Total estimated effort: 500+ hours across all identified hotspots.

## Recommended Approach

Structure this as a prioritised program, not a single initiative. Sequence by value: pages with highest user exposure, strongest evidence overlap, and greatest business impact (demo conversion, CS load reduction) first.

The Lead Grid and Distribution pages are the strongest candidates for the initial cycle: highest traffic, worst performance scores, most JS errors, and most frequently cited in CS reports and demo observations.

Technical note from the customer success team: DevExtreme has an Angular version, so migrating grids may not require full rebuilds from scratch. Some Client Portal CSS issues are already being addressed independently.
