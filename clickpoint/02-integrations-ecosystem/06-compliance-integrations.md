# Compliance Integration Suite — Technical Reference

Six third-party integrations built to validate leads against TCPA regulations, fraud signals, and data quality standards in real time. Each runs within LeadExec's sequential validation chain during the Post phase, with configurable stop/continue logic.

All integrations share the same architecture: webhook call during lead processing, response parsed and stored to lead log, criteria builder for automated actions (reject, flag, route to QC), and campaign-level enable/disable with Lead Type-level defaults.

This document serves as technical depth for understanding each integration's role in the validation chain. For the program-level overview, see [README.md](./README.md). For delivery details, see [04-delivery.md](./04-delivery.md).

---

## PureCallerID — Phone Validation and Compliance Risk

**What it does:** Validates phone numbers via the Aegis API Single Lead Loading endpoint. Returns status (Accepted/Rejected), reason (INACTIVE, INVALID, DNC, TCPA_FLAG, SUPPRESSED), and message.

**Why it matters:** First integration shipped, driven by AHS (Fortune 500 insurance client) requirement for real-time phone verification before lead delivery. Flags inactive numbers, DNC-listed numbers, and known TCPA litigation risks.

**Key details:**
- Trigger: HTTP POST during lead processing when enabled at campaign level
- Fields mapped: phone, zip, first name, last name, address
- Credentials: campaignId, listName, API token per client
- Future fields requested by AHS: activity_score, knownLitigatorCheck, carrier

**Origin:** AHS enterprise requirement. Normally billable scope, provided as courtesy given partnership value. Became the template for all subsequent integrations.

---

## TrustedForm — Consent Certificate Validation

**What it does:** Validates that inbound leads carry a valid TrustedForm consent certificate, confirming the lead was collected with proper disclosure and opt-in.

**Why it matters:** TCPA litigation increasingly hinges on proving consent. Without certificate validation, clients accept leads with no proof of compliant collection — a liability in regulated verticals like insurance and finance.

**Key details:**
- Validates certificate URL, age, and domain against TrustedForm's API
- Returns certificate status, consent verification, and page snapshot metadata
- Criteria: reject leads with expired, missing, or invalid certificates

---

## Trestle — Real Contact API

**What it does:** Validates phone numbers, email addresses, and physical addresses against authoritative data sources. Returns contact validity scores and enrichment data.

**Why it matters:** Goes beyond phone-only validation. Provides multi-signal contact verification that catches bad data across all lead fields, not just phone numbers. Particularly valuable for clients who deliver leads via multiple channels (phone, email, direct mail).

**Key details:**
- Validates: phone (landline/mobile/VOIP classification), email (deliverability), address (USPS standardisation)
- Returns: validity scores, line type, carrier, address components
- Criteria: configurable thresholds per field type

---

## DNC.com — Do Not Call Registry and Litigator Scrub

**What it does:** Two services in one integration. DNCScrub checks phone numbers against the National Do Not Call Registry. Litigator Scrub detects numbers associated with known TCPA litigants.

**Why it matters:** The most directly compliance-critical integration. Calling a number on the DNC registry or belonging to a known litigant can result in fines of $500-$1,500 per violation. For high-volume lead operations, this is existential risk.

**Key details:**
- DNCScrub: checks federal and state DNC registries
- Litigator Scrub: proprietary database of known TCPA litigants and their phone numbers
- Sequential position: typically runs after PureCallerID and before delivery
- Critical for insurance, home services, and financial services verticals

---

## IPQS — IPQualityScore Phone Validation and Fraud Scoring

**What it does:** Real-time phone fraud scoring. Returns a fraud_score (0-100) plus signals for VOIP, prepaid, recent abuse, carrier, and line type.

**Why it matters:** Complements compliance-focused integrations (DNC, PureCallerID) with fraud-focused signals. A phone number can be legally callable but still be a fraud vector — recycled number, VOIP spam, or synthetic identity. IPQS catches what compliance checks miss.

**Key details:**
- Fraud score ranges: below 75 = low risk, 75-85 = suspicious, above 85 = high risk, above 90 = very high risk
- Configurable strictness (0-2) per campaign
- VOIP and prepaid detection as separate boolean signals
- Typical latency: 300-500ms
- Criteria builder supports complex rules: "if fraud_score above 85, reject; if VOIP true, flag for QC"

---

## Abstract API — Email Validation

**What it does:** Validates email addresses for deliverability, format correctness, and disposable email detection.

**Why it matters:** Invalid or disposable email addresses waste delivery capacity and damage sender reputation. For clients who deliver leads via email or use email as a secondary contact channel, this prevents bounces and quality complaints.

**Key details:**
- Validates: format, MX record, SMTP deliverability, disposable domain detection
- Returns: deliverability score, is_valid, is_disposable, quality_score
- Lightest-weight integration in the suite — low latency, simple response structure

---

## How They Work Together

In a typical regulated lead flow, these integrations run sequentially during Post processing:

1. **PureCallerID** — Is this a valid, callable phone number?
2. **IPQS** — Is this phone number associated with fraud signals?
3. **DNC.com** — Is this number on the Do Not Call registry or associated with a TCPA litigant?
4. **TrustedForm** — Does this lead have valid consent documentation?
5. **Trestle** — Are the contact details (phone, email, address) verified against authoritative sources?
6. **Abstract API** — Is the email address deliverable?

If any integration rejects a lead and "skip remaining integrations" is enabled, subsequent calls are bypassed — saving processing cost and API credits. The order is configurable per campaign, and clients can enable only the integrations relevant to their compliance requirements.

Each integration's full JSON response is persisted to the lead log for audit and troubleshooting.
