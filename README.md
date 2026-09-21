# Construction Automation System

A modular automation system designed to connect construction lead capture, AI-powered data extraction, quotation workflows, workforce progress tracking, and customer follow-up into a connected operational workflow.

Built with **n8n, Airtable, Gmail, webhooks, and AI**.

---

## System Overview

The system is divided into five automation modules:

```text
Lead Capture & CRM
        ↓
AI Email Data Extraction
        ↓
Lead → Quote
        ↓
Workforce Task & Progress
        ↓
Review & Follow-up
```

Each module can operate independently while contributing to a broader construction business workflow.

---

## Automations

### 01 — Lead Capture & CRM

Captures new construction enquiries, prepares and structures lead information, stores the lead in Airtable, and sends email notifications and customer confirmations.

**Key capabilities:**

* Lead intake through webhook
* Lead data preparation
* Airtable CRM record creation
* Internal lead notification
* Customer confirmation email
* Initial lead status management

[View workflow →](./01-lead-capture-crm/)

---

### 02 — AI Email Data Extraction

Uses AI to extract structured construction enquiry information from incoming email content, reducing manual data entry and preparing information for downstream workflows.

**Key capabilities:**

* Email content processing
* AI-powered information extraction
* Structured construction lead data
* Parsing and normalization
* Data prepared for automation workflows

[View workflow →](./02-ai-email-data-extraction/)

---

### 03 — Lead-to-Quote

Transforms a qualified construction lead into a structured quote draft using AI-assisted construction analysis and automated quote preparation.

**Key capabilities:**

* Quote request detection
* Construction requirement analysis
* AI-assisted scope and assumption extraction
* Quote draft generation
* Quote approval workflow
* Automated customer quote delivery
* Quote status and validity tracking

[View workflow →](./03-lead-to-quote/)

---

### 04 — Workforce Task & Progress

Tracks workforce task progress against construction projects and updates project status based on operational commands.

**Key capabilities:**

* Task status updates
* Started / Completed / Delayed / Needs Help states
* Project progress calculation
* Workforce-to-project tracking
* Operational visibility

[View workflow →](./04-workforce-task-progress/)

---

### 05 — Review & Follow-up

Automates customer review and follow-up communication based on project completion and review status.

**Key capabilities:**

* Project record lookup
* Review eligibility checking
* Automated customer communication
* Review status tracking
* Prevention of unnecessary duplicate follow-ups

[View workflow →](./05-review-follow-up/)

---

## Architecture

The system uses n8n as the automation layer connecting external services and business data.

```text
                    ┌─────────────────────┐
                    │   Customer Enquiry  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Lead Capture / CRM │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │    AI Processing    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Quote Preparation │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Project / Workforce │
                    │      Tracking       │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Review & Follow-up  │
                    └─────────────────────┘
```

---

## Technology Stack

* **n8n** — workflow automation and orchestration
* **Airtable** — structured CRM and project data
* **Gmail** — automated business communication
* **OpenAI** — AI-powered extraction and construction analysis
* **Webhooks** — workflow triggers and external data intake
* **JSON / REST APIs** — system integration and data exchange

---

## Repository Structure

```text
construction-automation-system/
│
├── 01-lead-capture-crm/
│   ├── workflow.json
│   └── README.md
│
├── 02-ai-email-data-extraction/
│   ├── workflow.json
│   └── README.md
│
├── 03-lead-to-quote/
│   ├── workflow.json
│   └── README.md
│
├── 04-workforce-task-progress/
│   ├── workflow.json
│   └── README.md
│
├── 05-review-follow-up/
│   ├── workflow.json
│   └── README.md
│
└── README.md
```

Each module contains:

* The exported n8n workflow JSON
* A module-specific README explaining the workflow, architecture, nodes, inputs, outputs, and implementation details

---

## Important Note

The workflow exports in this repository have been sanitized for public sharing.

Credentials and authentication secrets are intentionally excluded.

To run the workflows, the required service credentials must be configured in the user's own n8n environment.

---

## Purpose

This project demonstrates how individual business automations can be designed as components of a larger operational system rather than isolated workflows.

The architecture can be adapted to different construction businesses, data structures, communication channels, and operational requirements.
