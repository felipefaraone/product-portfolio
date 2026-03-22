# Problem Framing — Integrations Ecosystem

## The Core Problem

LeadExec had no unified system for managing third-party lead validation services. Enterprise clients in regulated industries (insurance, home services, finance) needed real-time compliance checks against TCPA regulations, Do Not Call registries, phone fraud signals, and consent verification — but the platform offered no native way to do this.

The existing integrations page was minimal: users could enable a toggle and enter an API key. Everything else — field mapping, criteria configuration, response handling — had to be done manually at the individual campaign level. For a client running 100+ campaigns under the same Lead Type, this meant repeating identical configuration 100 times with no inheritance, no defaults, and no centralisation.

## Four Compounding Problems

**1. Lead quality degradation**
Without robust validation, poor-quality leads reached clients, damaging trust and revenue. There was no native way to filter invalid or risky leads before delivery.

**2. Regulatory exposure**
Clients in regulated verticals operated under strict TCPA rules. Inadequate compliance checks exposed both LeadExec and its clients to legal and financial risk. Calling a number on the Do Not Call registry or belonging to a known TCPA litigant could result in fines of $500–$1,500 per violation. For high-volume operations, this was existential risk.

**3. Fragmented management**
All configuration had to happen at the campaign level, one campaign at a time. Enterprise clients with hundreds of campaigns spent hours repeating the same field mappings and criteria setup.

**4. No processing logic**
There was no mechanism to process integrations in a sequential, configurable order. No way to say "run DNC check first, and if it fails, skip everything else to save cost."

## Why It Mattered Now

Three converging pressures created urgency:

**TCPA litigation exposure:** Regulatory risk was rising. Enterprise clients (particularly AHS, a Fortune 500 insurance company) were increasingly stringent about compliance proof before accepting lead delivery.

**Customer pain at scale:** As customers added more campaigns to their LeadExec fleets, the configuration burden became unbearable. A customer managing dozens of campaigns was productive; a customer managing hundreds was paralysed by repetition.

**Competitive vulnerability:** Competitors like LeadProsper, Boberdoo, and LeadsPedia already offered integration management. While their approaches weren't perfect, LeadExec was falling behind on a core capability.

## Who Was Affected

**Enterprise clients:** Particularly AHS, which had specific compliance requirements and the sophistication to demand better infrastructure. Other insurance, home services, and finance clients all faced the same TCPA exposure.

**The platform:** Reputation risk. When clients couldn't configure compliance checks easily, LeadExec bore some of the blame when bad leads were delivered to downstream partners.

**The product team:** The integration approach was fragile and slowing down delivery of new capabilities. Each new integration required writing documentation, guides, and troubleshooting paths separately for each customer context.

## Next Steps

Understanding this context is essential for evaluating the solution approach. See [02-discovery.md](./02-discovery.md) for how we investigated the problem and evaluated architectural options.
