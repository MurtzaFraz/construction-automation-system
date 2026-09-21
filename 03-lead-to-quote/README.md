# Construction AI Quote Automation

A two-stage AI-assisted construction quoting system built with **n8n, Airtable, OpenAI, and Gmail**.

The system takes a construction lead that has requested a quote, uses AI to analyse the project requirements, creates a structured quote draft for human pricing, and then sends the final quote to the customer after approval.

---

## Overview

Construction quoting often requires collecting information from customer enquiries, understanding the requested work, identifying missing information, preparing a structured scope, calculating pricing, and communicating the final quote.

This automation separates those responsibilities into two workflows:

**Workflow 1 — AI Quote Preparation**

Customer/lead data → Quote Requested → AI Analysis → Structured Quote Draft → Human Pricing

**Workflow 2 — Quote Approval & Sending**

Quote Draft → Human Approval → Final Quote Email → Customer

This creates a **human-in-the-loop quoting system** rather than allowing AI to independently determine final pricing.

---

## What This System Demonstrates

- AI-powered construction enquiry analysis
- Automated extraction of project requirements
- Structured quote preparation
- Missing-information detection
- AI-generated scope summaries
- Human-controlled pricing
- Approval-based automation
- Automated customer communication
- Airtable-based quote management
- n8n workflow orchestration
- OpenAI integration

---

## System Architecture

```text
Construction CRM
       │
       ▼
Quote Requested?
       │
       ▼
AI Construction Analysis
       │
       ▼
Parse AI Quote Data
       │
       ▼
Create Quote Draft
       │
       ▼
Human Pricing
       │
       ▼
Human Approval
       │
       ▼
Approved?
   ┌───┴───┐
   │       │
  YES      NO
   │       │
   ▼       ▼
Send      Wait
Quote
Email
```

---

## Workflow 1 — AI Quote Preparation

The first workflow monitors construction leads in Airtable.

When a lead is marked as requiring a quote, the workflow analyses the available project information using OpenAI.

The AI extracts and structures information such as:

- Customer details
- Project type
- Project location
- Project description
- Requested work
- Dimensions
- Materials
- Fixtures
- Assumptions
- Missing information
- Estimated duration
- Scope summary
- Quote readiness

The structured information is then stored as a quote draft in Airtable.

### AI Safety Rules

The AI is instructed to:

- Use the customer-provided information as the source of truth.
- Avoid inventing project requirements.
- Avoid inventing specific prices.
- Clearly identify assumptions.
- Identify missing information.
- Distinguish between known information and assumptions.
- Determine whether the available information is sufficient for pricing.

The AI prepares the information; it does **not** independently determine the final construction price.

---

## Workflow 2 — Quote Approval & Sending

The second workflow monitors the quote records for human approval.

When the quote is marked as approved:

```text
Approved = true
        │
        ▼
Send Quote Email
```

If the quote has not been approved, the workflow does nothing.

The final email can include:

- Customer name
- Project type
- Project location
- Quote ID
- Project scope
- Labour cost
- Material cost
- Other costs
- Total quote
- Quote validity
- Next-step instructions

This ensures that the customer only receives a final quote after human review and approval.

---

## Quote Lifecycle

```text
Lead
  ↓
Quote Requested
  ↓
AI Analysis
  ↓
Quote Draft
  ↓
Ready for Pricing
  ↓
Estimator Pricing
  ↓
Ready for Approval
  ↓
Human Approval
  ↓
Approved
  ↓
Quote Sent
```

---

## Technologies

| Technology          | Purpose                               |
| ------------------- | ------------------------------------- |
| n8n                 | Workflow automation and orchestration |
| Airtable            | CRM, quote data, and workflow state   |
| OpenAI GPT-4.1-mini | Construction enquiry analysis         |
| Gmail               | Automated customer communication      |
| JavaScript          | Data parsing and transformation       |

---

## Human-in-the-Loop Design

A key design principle of this system is that AI assists the quoting process without replacing human pricing decisions.

AI handles:

**Understanding → Structuring → Analysing → Identifying Missing Information**

The human estimator handles:

**Pricing → Review → Approval**

This allows the automation to reduce administrative work while keeping the final commercial decision under human control.

---

## Repository Structure

```text
Construction AI Quote Automation/
│
├── README.md
│
├── docs/
│   └── technical-documentation.md
│
└── workflow/
    ├── construction-ai-quote-preparation.json
    └── construction-quote-approval-sending.json
```

---

## Technical Documentation

Detailed technical documentation is available here:

`docs/technical-documentation.md`

The documentation covers the workflow architecture, nodes, AI processing, data flow, approval logic, error handling, security considerations, limitations, and future improvements.

---

## Workflow Files

### AI Quote Preparation

`workflow/construction-ai-quote-preparation.json`

Handles AI analysis and creation of the structured quote draft.

### Quote Approval & Sending

`workflow/construction-quote-approval-sending.json`

Handles human approval and automated delivery of the final quote.

---

## Project Status

**Prototype / Demonstration System**

This project demonstrates how AI and workflow automation can be combined to create a structured construction quoting process with human oversight.

It is designed as an automation demonstration and can be extended with additional CRM, estimating, document-generation, notification, and reporting capabilities.

---

## Future Improvements

Potential future extensions include:

- Automated quote document generation
- PDF quote creation
- Additional customer communication channels
- Automated follow-up sequences
- More advanced construction cost estimation
- Document and attachment extraction
- CRM integration
- Customer approval workflows
- Quote status notifications
- Reporting and analytics

---

## Related Construction Automation System

This quote automation is part of a larger construction automation system covering:

1. Lead Capture & CRM
2. AI Email Enquiry Extraction
3. AI Quote Preparation & Approval
4. Workforce Task & Progress Tracking
5. Review & Follow-up Automation

Together, these workflows demonstrate how multiple business processes can be connected into a single construction operations system.
