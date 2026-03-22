# MCP AI Assistant — MVP Pitch

## Problem

Configuring Delivery Accounts and Delivery Methods in LeadExec is one of the most support-intensive parts of onboarding.

The flow is technical, sequential, and unforgiving — requiring credentials, mapping accuracy, time-zone logic, and multiple validation steps. First-time users often get stuck or restart the process multiple times. Customer Success and Integrations teams consistently report high onboarding friction and frequent intervention.

The numbers:
- **73%** of new users reported confusion or drop-off during setup
- **40%+** of first delivery attempts failed due to missing credentials, incorrect mapping, or out-of-sequence configuration
- Only **~35%** of trial accounts reached full delivery readiness (target: 75%)
- Setup cycles extended days beyond initial signup, requiring manual CS support
- SMB customers frequently lacked the technical depth to complete setup unaided

This slows activation, increases support load, and delays value realisation.

## Opportunity

Most onboarding requests follow predictable patterns that can be automated when exposed via existing LeadExec REST APIs.

With the introduction of the MCP (Modular Command Processor) server and internal AI capabilities, there is a clear opportunity to test whether an AI-guided assistant can execute those configurations safely and reliably.

The goal for this MVP is not feature completeness, but to prove that AI can perform delivery setup correctly, safely, and end-to-end inside the live product.

## Scope and Appetite

We will intentionally avoid a full UI rewrite or large-scale replatforming.

The MVP will focus on four essential entities:
- **Clients** — buyer entities and contact information
- **Lead Types** — data schema selection for delivery configuration
- **Delivery Methods** — delivery channels (email, FTP, POST, SOAP, webhook)
- **Delivery Accounts** — buyer destinations and routing rules

The MVP will use existing LeadExec REST APIs, wrapped in a .NET-based MCP Server, with schema-bound tool calls exposed to the AI Assistant. The assistant will run in a feature-flagged chat interface inside LeadExec, accessible to internal testers and select accounts.

Initial appetite is 2–3 weeks, with understanding that actual delivery may vary based on foundation complexity and integration learning.

## Solution Architecture

### MCP Server Layer
.NET Core service exposing safe CRUD tools for Delivery entities. JSON schema validation and input normalisation at tool boundary. Strict isolation between resources (Accounts, Methods, Enums).

Tools: `create_delivery_account`, `update_delivery_account`, `get_delivery_account`, `create_delivery_method`, `update_delivery_method`, `get_delivery_method`, `get_usa_states`, `get_lead_type`.

### Prompt Architecture
Declarative XML-based resource prompts defining exact user flow. Three-layer system: Global AI System Prompts (behaviour rules), AI Actions (workflow entry points), AI Resources (atomic phases). ASK/RETRY logic for required inputs — no inference or skipped steps. Full detail in [06-prompt-strategy.md](./06-prompt-strategy.md).

### Assistant Behaviour
Driven by global system prompt hierarchy enforcing workflow integrity. Automatically progresses through all required phases until completion. Summarises configurations and confirms before activation. On error, retries or requests clarification rather than proceeding with uncertain data.

### Adaptive Cards Framework
Structured input collection for enumerations, dropdowns, and confirmations. Text-based collection for free-form data (names, URLs, credentials).

### UI Integration
Embedded side-panel chat in LeadExec (Angular frontend). Uses existing authentication and session handling. Feature-flagged at account level. Entry point contextual on relevant screens, not global toolbar.

## Success Metrics

### What This MVP Measures
- Total assistant sessions
- Sessions per user and per company
- Number of Client Setup starts
- Number of Delivery Method Setup starts
- Number of Delivery Account Setup starts

### What Future Cycles Will Add
- Flow completion rate
- Drop-off analysis
- Validation error tracking
- Model fallback frequency
- Per-company adoption trends
- Correlation between assistant usage and trial-to-paid conversion

## Next Iteration Direction

The next cycle will expand from guided setup to context-aware onboarding:
- Client creation flow with Lead Type association
- Connection testing for Webhook and FTP
- Summarisation memory for multi-session continuity
- Usage tracking and telemetry dashboards
- Support for additional delivery types (Ping/Post)
- Gradual feature-flag exposure toward public beta

---

## Next Steps

See [02-discovery.md](./02-discovery.md) to understand how we validated this approach. See [04-delivery.md](./04-delivery.md) for how we executed against this proposal and managed scope changes.
