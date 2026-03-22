# MCP AI Assistant — Delivery and Key Decisions

This document covers the four core decisions that shaped execution, how scope evolved in response to real testing, and how we managed complexity and stakeholder expectations throughout delivery.

See [03-pitch.md](./03-pitch.md) for the original MVP proposal.

---

## Decision 1: Existing APIs Over Full Angular Rewrite

**Constraint:** The legacy Delivery screens were built on ASP.NET WebForms and DevExtreme grids with no REST API surface. A complete replatforming to Angular would have taken ~6 weeks.

**The decision:** Push the team to explore what was already possible via existing REST endpoints in other parts of LeadExec. Build the first MCP Agent using current infrastructure.

**Rationale:** Waiting for a major replatforming effort would have delayed the entire initiative. Using existing APIs let us move to a working prototype weeks earlier.

**Tradeoff:** Some Delivery entity fields weren't API-accessible, which limited certain configuration options in the MVP. This was an acceptable constraint for the initial release.

**Outcome:** This decision unblocked the entire project. We moved forward with a working prototype while keeping the long-term option of deeper UI investment if the assistant proved valuable.

---

## Decision 2: Deterministic Guided Flow Over Free-Form Chat

**Constraint:** The first prototype used open conversational interaction where the assistant conversed naturally and executed tool calls based on context.

**What broke:** During testing, this proved unreliable for structured onboarding. Users could skip required steps or provide partial inputs. Sequential dependencies were hard to enforce. QA teams found it difficult to reproduce and verify flows consistently.

**The decision:** Shift to a guided hybrid model — conversational tone combined with structured Adaptive Cards enforcing predictable sequence, input validation, and schema integrity.

**Rationale:** Structured guidance is not the opposite of good UX. It's a prerequisite for safety in configuration workflows. The assistant could still sound natural while following clear, enforced steps.

**Implementation impact:** Adaptive Cards were initially treated as a small UX enhancement. In practice, they became central to the experience — handling dropdowns, confirmations, and structured previews that could not be managed safely through text alone. Their adoption required a dedicated feasibility spike and significant development effort.

**Outcome:** This was the architectural decision that made the product shippable rather than just demo-able. It transformed the product from an experimental chat into a guided onboarding experience that users could trust.

---

## Decision 3: Scope Expansion to End-to-End Onboarding

**Discovery:** Once real testing began, a fundamental dependency surfaced: Delivery setup cannot exist without a Client. New customers entering LeadExec needed to create their Client entity as the first step. Without this, the assistant could complete only part of the setup, leaving users blocked mid-process.

**Scope additions:**
1. **Client creation** — enabled end-to-end onboarding from a blank state
2. **Lead Type selection** — Delivery setup required knowing which Lead Type the Delivery was associated with
3. **Field mapping preview** — users needed to map fields between LeadExec data and destination systems

**Rationale:** The original MVP (Delivery Accounts + Methods only) couldn't deliver actual user value. Each addition expanded scope but ensured the assistant reflected the actual product workflow rather than an artificial slice of it.

**Timeline impact:** The original 2–3 week estimate was now clearly insufficient. This expansion added ~4 weeks.

**How we managed it:** I owned this conversation with the CEO by documenting exactly why each expansion was necessary and what the product would lose without it. The reframing: "We can ship the narrow version, but it will be a tech demo, not a product. The expanded version proves AI can actually onboard a customer." This preserved alignment and shared ownership of the decision.

---

## The Execution Learning Loop

The majority of development time after these core decisions was consumed by a continuous learning cycle that is normal when working with new technology where the team has no prior experience.

**The pattern:**
1. Prompt tested → new backend limitation surfaced
2. Model skipped a phase or passed invalid data
3. Developers strengthened the MCP architecture
4. Retest revealed new discovery → cycle repeats

**What changed in the backend:**
- State persistence to remember configuration progress
- Validation and normalisation layers to prevent schema drift
- Refined tool outputs to keep AI communication deterministic
- Trimmed schema payloads to stay within model token limits
- Modularised instructions into `mcp://resource/...` files

This was not rework from poor planning. It was architectural learning in real time. Discovery and delivery overlapped because they had to — the team was navigating genuinely new technical territory where prior experience didn't exist.

**Model stability:** As workflows grew, the model began truncating messages or dropping steps under large payloads. This was mitigated through payload trimming, prompt modularisation, and tighter prompt discipline.

---

## Managing Timeline Extension and Stakeholder Expectations

**What happened:** Development began August 5, 2025. Global feature flag activation: October 27, 2025. The initial 2–3 week estimate extended to roughly ten weeks.

**Why this happened — honest framing:**

The project took longer not because of execution delays, but because every layer evolved through real usage. When working with genuinely new technology where the team has no prior experience, discovery and delivery overlap. I recognised this, documented it transparently, and used it to set realistic expectations for future phases.

**What I did:**

When the timeline extension became clear, I wrote a detailed document explaining exactly why — the foundation complexity, the learning loop between prompts and backend, the scope expansion rationale, and the model stability challenges.

Rather than hiding the miss, I reframed the conversation from "the team is behind" to "this is the cost of building a real product, not a demo." This preserved team trust, helped stakeholders understand the complexity, and set realistic expectations for next-cycle planning.

**The foundation was more complex than expected:** The first two weeks were consumed setting up the MCP Server, wiring it to LeadExec, and exposing CRUD operations for Delivery entities. That work alone equalled the original estimate for the entire MVP. Only after the environment stabilised could prompt testing begin.

**UI and Adaptive Cards added unexpected depth:** DevExtreme was rejected for customer-facing use, forcing a parallel custom chat UI implementation.

**Product readiness added parallel work:** Beyond the AI logic, the MVP required an AI Administration panel, conversation logging, UI entry points, feature flag infrastructure, KB article, and go-to-market materials — none in the original 2–3 week plan.

---

## What This Demonstrates

This delivery arc shows PM competence in navigating uncertainty, not the problems themselves. The work involved:

1. **Clear-eyed scope decisions** — expanding scope when necessary, but always with transparent rationale
2. **Architectural learning** — recognising that new technology requires overlapped discovery and delivery
3. **Stakeholder honesty** — reframing delays as learning, not failure; preserving trust through transparency
4. **Deliberate decision documentation** — ensuring each tradeoff was intentional and understood across the team

The project didn't ship in 2–3 weeks, but it shipped something that proved the concept, was production-ready, and established a foundation strong enough for the next cycle.

---

## See Also

- [03-pitch.md](./03-pitch.md) — the original MVP proposal
- [08-scope-evolution.md](./08-scope-evolution.md) — detailed narrative of each scope addition
- [05-results.md](./05-results.md) — what we shipped and what users experienced
- [07-retrospective.md](./07-retrospective.md) — team retrospective and learning themes
