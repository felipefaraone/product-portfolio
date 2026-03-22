# MCP AI Assistant — Retrospective

**November 2025 · Post-MVP reflection**

---

## What Went Well

### Strong foundation through iterative build-and-learn
The team built a production-ready system through continuous iteration. Each prompt test exposed architectural gaps that were resolved in real time, producing reusable infrastructure — state persistence, validation layers, refined tool schemas, modularised prompts — that would have been impossible to design upfront.

### Team collaboration under genuine uncertainty
Cross-functional cooperation between product, design, and engineering enabled fast problem-solving on a project where nobody had prior experience with the technology. Trust and adaptability within the team were essential to shipping despite evolving requirements.

### Durable technical learning
The team built deep understanding of AI workflows, MCP architecture, and prompt design that extends well beyond this project. The prompt framework, Adaptive Cards system, and MCP integration patterns are all carrying forward into future product work.

---

## What I Would Do Differently

### 1. Build in a discovery spike before committing to timelines with new technology
When working with genuinely new technology where the team has no prior experience, I would now build in a dedicated discovery spike before committing to any timeline. We treated estimates as commitments when they should have been hypotheses. The original 2–3 week estimate was based on assumptions about MCP complexity that proved wrong within the first week. A structured spike — even one week — would have surfaced the real scope before external expectations were set.

### 2. Define the intended interaction model before any build starts
I would align the team on the intended experience shape — chat, wizard, or agentic flow — before writing the first line of code. We discovered mid-project that our assumptions about the UX model were not shared across product, design, and engineering. What began as a conversational AI concept evolved into a guided hybrid model once testing revealed the need for structure and validation. That pivot was correct, but it caused rework that earlier alignment would have prevented.

### 3. Assign prompt engineering ownership explicitly from day one
Prompt creation and maintenance sat between product, design, and engineering without a clear owner. In future AI projects, I would define this responsibility explicitly from the start — who writes prompts, who reviews them, who maintains them as the backend evolves. The ambiguity slowed iteration cycles and made it harder to maintain consistency as the prompt architecture grew.

---

## Why the MVP Took ~10 Weeks Instead of 2–3

Development began August 5, 2025. Global feature flag activation: October 27, 2025.

The timeline extended for three reasons, each of which was managed deliberately rather than discovered late.

**The technical foundation was more complex than expected.** The team had no prior experience with MCP. Setting up the server, wiring it to LeadExec, and exposing CRUD operations for Delivery entities consumed the first two weeks — equal to the original estimate for the entire MVP. Only after the environment stabilised could real prompt testing begin.

**The original MVP scope was too narrow to deliver real value.** Delivery Accounts and Methods alone couldn't produce a usable onboarding flow. I made the call to expand scope to include Client setup, Lead Type selection, mapping previews, and summary validation. This turned a technical demo into a genuine product, but added several weeks. I owned this decision in conversations with the CEO, documenting the rationale and reframing expectations.

**Discovery and delivery overlapped continuously.** Each prompt test surfaced backend limitations — the model would skip a phase, pass invalid data, or truncate messages under growing payloads. Developers strengthened the MCP architecture in response. This was not rework. It was architectural learning happening in real time, and it produced the validation layers, state persistence, and deterministic execution model that made the system production-ready.

For the PM perspective on managing stakeholder expectations through this timeline, see [04-delivery.md](./04-delivery.md).

---

## Key Takeaways

1. **Design for uncertainty.** When working with new technologies, assume scope and effort will evolve. Build flexibility into planning instead of treating estimates as fixed commitments.

2. **Clarify ownership of AI workflows.** Define who owns prompt logic, testing, and iteration loops early. Consistent accountability avoids ambiguity as the system grows.

3. **Iterate with clarity.** Each iteration should have explicit goals — what is being validated, what is out of scope, and what learning it should produce.

4. **Align early on experience philosophy.** Before building, ensure everyone shares the same vision of how users will interact with the product.

5. **Connect technical learning to product value.** Every architectural enhancement should clearly link to how it improves the user experience.

6. **Invest in reusable learning.** Treat each experimental project as a foundation. Document what worked, what didn't, and carry that maturity forward.

---

The MVP was not just a prototype proving AI could call tools — it was a fully guided, production-ready onboarding flow, tested end-to-end and built on a foundation strong enough to support future iterations.
