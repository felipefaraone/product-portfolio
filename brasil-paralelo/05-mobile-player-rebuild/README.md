# Mobile Player Rebuild — DRM Infrastructure as Product Investment

**Brasil Paralelo · 2022 · Shape Up Big Batch (6 weeks, single engineer)**

---

## Problem

The existing mobile player could not support DRM (contractually required for all licensed content) and could only render in fullscreen — blocking both the new catalogue UX and the Downloads feature. Mobile was 50% of DAU. The player was the core interaction surface. This was non-negotiable infrastructure work.

---

## My Role

Sole PM. Built the business case, defined the scope boundaries, and managed a critical cross-squad dependency that surfaced mid-cycle.

---

## Key Decisions

**1. Financial case to justify a single-engineer Big Batch**

Infrastructure work competes poorly for resources against user-facing features. I built the financial case: R$374,920 (~AUD 105K at 2022 rates) Year 1 PBT and R$8.8M (~AUD 2.5M at 2022 rates) lifetime value from enabling DRM content deals and supporting the GBB tier structure. This reframed the player rebuild from "technical debt" to "product investment with quantified return."

**2. Eight explicit no-goes protecting focus**

With a single engineer for 6 weeks, scope discipline was existential. I defined 8 explicit no-goes in the Shape Up pitch — features and improvements that were deliberately excluded to protect the one engineer's focus on the core deliverable. Every no-go was a real request from someone in the organisation.

**3. BetterPlayer selection over alternatives**

After evaluating player libraries, selected BetterPlayer for its DRM support, flexibility, and long-term maintainability. This was a technical decision with product implications: the chosen library needed to support fullscreen, mini-player, and stick mode to enable the catalogue UX redesign planned for the following quarter.

**4. Cross-squad DRM dependency management**

The DRM license endpoint format change was a dependency on the backend team that was not visible at pitch time. I coordinated directly with the backend squad to align the endpoint spec with the mobile player's requirements, preventing a late-cycle blocker.

---

## Results

- Crash-free rate: **97.2% → 99.1%** (+1.9pp)
- Session length: **+18%**
- DRM support unlocked **2 new content distribution agreements**
- Mini-player and stick mode enabled for subsequent catalogue UX redesign

![Player prototype demonstrating multi-context rendering (fullscreen, mini-player, stick mode)](images/player-prototype-multi-context.gif)

---

## Documentation

| Document | What it covers |
|----------|---------------|
| [01-pitch.md](./01-pitch.md) | Full Shape Up pitch: financial case, appetite, solution, rabbit holes, no-goes, and measured outcomes |
