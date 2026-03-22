# MCP AI Assistant — Scope Evolution

As the project progressed, real-world testing and team feedback revealed important gaps between the original chat-based concept and what was truly needed to deliver value to customers.

Each of the following adjustments emerged from discovery — not as unplanned scope expansion, but as essential refinements to make the assistant usable, complete, and production-viable.

For the strategic PM perspective on managing these scope changes and stakeholder expectations, see [04-delivery.md](./04-delivery.md).

---

## 1. From Chat to Guided Hybrid Flow

The first prototype followed free-form chat interaction where the AI conversed naturally and executed tool calls.

During testing, this proved unreliable for structured onboarding: users could skip required steps or provide partial inputs, it was difficult to validate sequential dependencies, and QA teams found it hard to reproduce flows.

The assistant evolved into a guided hybrid model — conversational tone combined with structured Adaptive Cards enforcing predictable order, input validation, and schema integrity. This transformed it from a demo chat into a guided onboarding experience.

## 2. Addition of Client Creation

Early iterations revealed a fundamental dependency: Delivery setup cannot exist without a Client. Originally, Client management was assumed to be preconfigured manually, but in practice new customers needed to create their Client entity as the first onboarding step.

Without this capability, the assistant could only complete part of the setup, leaving users blocked mid-process. Adding Client creation enabled a truly end-to-end onboarding path.

## 3. Inclusion of Lead Type Selection and Field Mapping

Once Client creation was introduced, testing showed that Delivery setup required knowledge of which Lead Type the Delivery was associated with. Users also needed to map fields between LeadExec data and destination systems. These steps mirrored the real configuration path and ensured the assistant reflected the actual product workflow.

## 4. Backend Reinforcement and State Persistence

As prompt testing intensified, limitations surfaced between the AI layer and backend logic: prompts occasionally lost context, skipped phases, or passed invalid payloads. The team introduced state persistence, validation and normalisation layers, and refined tool outputs to keep AI communication deterministic. These changes strengthened the MCP architecture and turned the AI flow into a reliable framework.

## 5. Introduction of Adaptive Cards Framework

Adaptive Cards were initially a small UX enhancement. In practice, they became central — handling structured inputs, dropdowns, and previews that could not be managed safely through text alone. Their adoption required dedicated development and formatting logic but significantly improved usability, validation, and clarity.

## 6. Expanded Prompt and Tool Definitions

As new steps were added (Client, Lead Type, Mapping), prompt definitions evolved into modular XML resources linked to their respective MCP tools. This refactoring introduced a consistent prompt framework that scales across future AI features.

---

## Summary

These changes collectively transformed the MCP AI Assistant from a conversational prototype into a structured onboarding flow capable of completing real-world configurations.

While the initial vision aimed to prove AI could call tools, the evolved version proved that AI could onboard a customer — safely, coherently, and end to end.
