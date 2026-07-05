# MCP AI Assistant — Discovery Journal

This document traces the full discovery journey from initial feasibility assessment to architectural decisions, UX pivots, and scope evolution across ~12 weeks of iterative development.

Unlike a Shape Up pitch written before build begins, this discovery unfolded in parallel with implementation — each week of testing revealed new constraints that reshaped the product.

See [01-problem-framing.md](./01-problem-framing.md) to understand the business context. See [03-pitch.md](./03-pitch.md) for the MVP proposal that emerged from this discovery.

---

## Week 1: Technical Feasibility (July 28–31, 2025)

### Starting question
Can the MCP Server automate Delivery Account and Delivery Method creation inside LeadExec by calling existing APIs?

### What we found
The Delivery Account and Delivery Method UIs are not built in Angular. They run on legacy ASP.NET WebForms with DevExtreme grids, tightly coupled to server-bound forms. Save and Update actions perform form submissions via .NET model binding — no REST API calls were observable in browser dev tools. Authentication relies on session cookies and .NET Forms Authentication, not JWT tokens.

Some parts of LeadExec (Integrations settings) are built in Angular and communicate via REST endpoints — already consumable by MCP. But the delivery workflow sits entirely outside this boundary.

### Conclusion
Delivery configuration cannot currently be automated via MCP due to lack of REST APIs and reliance on legacy postback-based workflows. The DevExtreme DataGrid implementation adds complexity to any future migration.

### Decision
The lead engineer estimated ~6 weeks for full Angular conversion. The team decided not to pursue UI refactors at this stage. Instead: explore what is already possible via existing REST endpoints.

---

## Week 2: Scoping the Feasible Path (Aug 1–4, 2025)

The project manager and designer completed API-level discovery for existing endpoints, confirming that enough REST surface existed to build a meaningful first version. The lead engineer confirmed the need for additional backend work to host an MCP server in the .NET/Azure environment.

### Agreed direction
- No full Angular conversion
- Build the first MCP Agent using current REST APIs
- Start with delivery setup flows
- Move to more complex entities later if successful

---

## Week 3: Architecture and UI Decisions (Aug 14–25, 2025)

### Chat UI debate
Two options: DevExtreme chat component (fast but limited) vs. custom-built UI (slower but extensible). The lead engineer was firm: DevExtreme would not be exposed to customers. Decision: custom implementation for production, with the understanding that this added development time.

### MVP scope defined
- Delivery Accounts and Delivery Methods only
- Launcher button opens right-side chat panel
- Conversational flows guide users through setup
- Feature-flagged rollout to trusted testers
- Out-of-scope questions redirect to KB or Intercom

### AI vision exploration
The CEO, the designer, and I explored the long-term AI direction for LeadExec. Two perspectives emerged:

**The designer's direction:** Platform-first — expand MCP broadly into external agents, BI exposure, conversational insights, automation, forecasting.

**The CEO's direction:** PLG-first — move beyond Delivery into Inbound setup + Attribution, so users see CPA/ROI early. Add nudges and playbooks to accelerate trial activation.

**Working synthesis:** Maximise customer activation in the near term (the CEO) while preserving MCP-first architecture for long-term extensibility (the designer). Sequence: Delivery hardening → Inbound capture → Basic Attribution → Activation UX → Conversational Insights → Optimisation.

This vision framing influenced how we positioned the MVP internally — not as a standalone feature, but as the first step in a multi-phase AI strategy. See [03-pitch.md](./03-pitch.md) for the proposal that emerged.

---

## Weeks 4–5: First Prototype and the Chat-to-Guided Pivot (Aug 25 – Sep 8, 2025)

### What we built
A working chat interface connected to the MCP Server, capable of creating Delivery Accounts and Methods via conversational prompts.

### What broke
Free-form chat interaction proved unreliable for structured onboarding:
- Users could skip required steps or provide partial inputs
- Sequential dependencies (Delivery Account before Method) were hard to enforce
- QA teams couldn't reproduce and verify flows consistently

### The pivot
The assistant evolved from free-form chat into a **guided hybrid model** — conversational tone combined with structured Adaptive Cards for each configuration step. This preserved the natural language interface while enforcing predictable order, input validation, and schema integrity.

This was the single most important architectural decision in the project. It transformed the product from a demo chat into a guided onboarding experience. See [08-scope-evolution.md](./08-scope-evolution.md) for how this pivot shaped subsequent decisions.

### Adaptive Cards surprise
Initially treated as a small UX enhancement. In practice, they became central to the user experience — handling dropdowns, confirmations, and structured previews that could not be managed safely through text alone. Their adoption required a dedicated feasibility spike and significant development effort.

---

## Weeks 6–8: Scope Expansion (Sep 8 – Sep 29, 2025)

### Discovery: Delivery setup alone is not enough
Once real testing began, a fundamental dependency surfaced: Delivery setup cannot exist without a Client. New customers entering LeadExec needed to create their Client entity as the first step. Without this, the assistant completed only part of the setup, leaving users blocked mid-process.

### Additions
1. **Client creation** — enabled end-to-end onboarding from a blank state
2. **Lead Type selection** — Delivery setup required knowing which Lead Type the Delivery was associated with
3. **Field mapping preview** — users needed to map fields between LeadExec data and destination systems

Each addition expanded scope but ensured the assistant reflected the actual product workflow.

### Managing the timeline
The original 2–3 week estimate was now clearly insufficient. I owned this conversation with the CEO by documenting exactly why each expansion was necessary and what the product would lose without it. The reframing: "We can ship the narrow version, but it will be a tech demo, not a product. The expanded version proves AI can actually onboard a customer."

---

## Weeks 8–10: The Learning Loop (Sep 29 – Oct 20, 2025)

### The pattern that consumed most of the time
Each time a prompt was tested, new backend limitations surfaced. The model might skip a phase, pass invalid data, or lose context between tool calls. To fix this, developers repeatedly strengthened the MCP architecture:

- State persistence to remember configuration progress
- Validation and normalisation layers to prevent schema drift
- Refined tool outputs to keep AI communication deterministic
- Trimmed schema payloads to stay within model token limits
- Modularised instructions into `mcp://resource/...` files

This was not rework. It was architectural learning in real time. The cycle — prompt → failure → backend fix → retest → new discovery — drove the majority of development time in the second half of the project.

### Model stability
As workflows grew (Lead Type lookups, field mappings, cards), the model began truncating messages or dropping steps under large payloads. Mitigated through payload trimming, prompt modularisation, and tighter prompt discipline.

---

## Week 10+: Production Readiness and Launch (Oct 20–27, 2025)

### Parallel workstreams
Beyond the AI logic, the MVP required:
- AI Administration panel in dev and production
- Conversation and tool logging for QA and observability
- UI entry points for AI Assist within LeadExec
- Controlled rollout infrastructure via feature flags
- KB article draft for CS review
- Go-to-market materials and marketing video

### Global activation
Feature flag enabled for all users on October 27, 2025.

### Post-launch: Strategic reassessment (March 2026)
After several months in production I wrote a strategic analysis ("AI in LeadExec: current state, key insights, and recommended direction") that assessed what the assistant had and had not proven. It proved the technical thesis: real usage across multiple companies, zero unsafe tool calls in production, and setup time down from days to under fifteen minutes. It did not prove the business thesis: telemetry showed no sustained lift in activation or CS effort, because the assistant covered only a slice of the setup and users finished the rest manually. I treated that as a signal, not a verdict. Recommended direction: keep the assistant stable and pivot toward smaller embedded helpers on specific high friction tasks (field mapping, posting instructions parsing, lead type generation), a more defensible direction aligned with where LLMs are strong today. Separating the proven technical thesis from the unproven business one, and acting on it, was the real output of the project.
