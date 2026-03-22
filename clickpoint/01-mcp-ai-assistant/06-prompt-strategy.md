# MCP AI Assistant — Prompt Strategy

The MVP introduced the full prompt execution framework that connects the MCP AI Assistant to LeadExec through the AI Administration Dashboard.

The goal was to define a deterministic, multi-phase prompting model that the assistant could follow end to end without improvisation.

This is the technical depth document. For context on why this architecture was necessary, see [04-delivery.md](./04-delivery.md). For the full product narrative, see [05-results.md](./05-results.md).

---

## Architecture: Three Layers

### Layer 1: Global AI System Prompts
Define the assistant's global behaviour, communication rules, and reasoning hierarchy. These apply across all workflows.

Key rules enforced:
- Follow strict instruction hierarchy: (a) protect workflow integrity, (b) enforce tool discipline, (c) maintain user-facing clarity
- Persist through all required phases until completion — never skip or end mid-flow
- Collect required inputs explicitly (ASK), validate before tool execution, never infer missing data
- Keep technical operations hidden — expose only user-friendly terms (client name, delivery type)
- Apply normalisation for emails, URLs, states, numbers, and booleans before payload submission
- Use `display_adaptive_card` for structured inputs; plain text for conversational fields
- Run quality gates before declaring completion: confirm entities and credentials exist and collected data matches what is persisted

### Layer 2: AI Actions
Each AI Action defines the entry point for a setup flow. Examples: Create Single Client Setup, Setup Delivery Method, Create Delivery Account.

An action controls flow detection, routing, and initial phase transition. Actions act as orchestration nodes that determine which resource sequence to execute next.

### Layer 3: AI Resources
Each AI Resource defines one atomic phase in the workflow. A resource contains:
- The MCP tool to execute
- User prompts and input collection method (text or adaptive card)
- Variables to retain and pass forward to the next phase
- Validation logic and transition via `ON_COMPLETE`

Example resource definition:
```xml
<phase_2_get_lead_types>
  TOOL: get_lead_types
  PROMPT: "Please select a Lead Type for this client."
  ASK [adaptive_card]: leadTypeName (leadTypeUID)
  RETAIN: leadTypeUID, leadTypeName, leadFields
  ON_COMPLETE: mcp://resource/phase-3-create-delivery-method
</phase_2_get_lead_types>
```

Each phase executes independently and calls the next through its `ON_COMPLETE` directive, enforcing full determinism.

---

## Execution Model

1. The user starts an AI Action
2. The assistant determines intent and loads the first AI Resource
3. Each phase executes its defined MCP tools in order
4. The flow auto-progresses until all required entities are created and validated
5. If any prerequisite fails, the assistant re-prompts or retries rather than skipping steps

---

## Design Rationale

- **Machine-readable and declarative.** No logic hidden in the LLM. Every step is defined explicitly in the prompt framework.
- **Completion enforced.** The workflow engine ensures users can't abandon incomplete entities mid-flow.
- **Consistent data entry.** Adaptive Cards keep structured input collection uniform across all setups.
- **Clean data guaranteed.** Normalisation rules ensure valid data before any API call.
- **Modular and extensible.** Resource structure allows adding telemetry, memory, and new workflows without redesign.

---

## Outcome

This prompt framework enabled deterministic multi-step automation while preserving safe user control. It also established a standardised format for all future AI workflows in LeadExec — reusable, testable, and transparent.
