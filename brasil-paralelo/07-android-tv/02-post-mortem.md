# Android TV — Post-Mortem

## Results

| Store | Downloads | Date |
|-------|-----------|------|
| Google Play (Android TV) | **2,765** | By May 7, 2022 |
| Amazon Appstore (Fire TV Stick) | **728** | By May 8, 2022 |

Gradual rollout to 100% of eligible users completed on April 21, 2022.

![Android TV downloads — Google Play Console](images/android-tv-google-play-downloads.png)
![Fire TV Stick downloads — Amazon Developer Console](images/fire-tv-amazon-downloads.png)

---

## What Worked

- **Beta group:** A pre-launch beta group helped catch bugs before the public release — and also retained some members who were considering cancellation.
- **Hebe + native integration:** Reusing the web TV app as the UI layer worked well — the same interface serves Samsung, LG, and now Android TV.

---

## What Didn't Work

- **Assumptions didn't hold:** Several technical assumptions were validated only after work had started. This is a niche development area with limited community documentation.
- **App store friction:** Both the Play Store and Amazon Appstore provided vague rejection messages, requiring iterative guesswork to resolve compliance issues. Having a contact or liaison inside the partner companies would have helped significantly.
- **Context switching:** Team members were juggling multiple concurrent projects, reducing focus and velocity on this one.
- **Missing progress tracking:** No cycle tracking document was maintained during the project — progress was invisible until retrospectives.
- **Team continuity:** The engineer with the most context on Hebe was not fully dedicated to this project and later left the company, creating a knowledge gap.
- **Mid-project scope change:** A scope change mid-cycle interrupted planned work.

---

## Lessons

| Area | Learning |
|------|---------|
| Discovery | Deep-dive on store submission processes before committing to a timeline — app store policies are non-trivial and poorly documented |
| Partnerships | For platform expansion into 3rd-party ecosystems, identify an internal contact at the platform company before launch |
| Team allocation | Whoever owns the core layer (e.g., Hebe) needs dedicated time on new platform initiatives that depend on it |
| Tracking | Cycles need visible week-by-week tracking — status should be shared proactively, not reconstructed retrospectively |
| Scope | Define scope changes as explicit decisions with documented tradeoffs, not ad-hoc mid-cycle pivots |

---

## Process Improvements

- Create a reusable platform-launch checklist (covering: app store requirements, beta process, analytics ownership, release strategy) for future platform expansion projects.
- Cycle tracking document to be visible in real time, not reconstructed retrospectively.
- Platform expansion dependencies should be explicit in the Shape Up pitch: which internal team owns the core layer, what dedicated time they need, what handoff points exist.
