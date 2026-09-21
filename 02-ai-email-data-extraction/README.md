# Construction AI Email Enquiry Assistant — V1

## 1. Overview

The Construction AI Email Enquiry Assistant is an AI-powered email processing workflow designed for construction and home-improvement businesses.

The system monitors an incoming Gmail inbox for customer enquiries, sends the enquiry to an AI model for structured extraction and classification, parses and validates the AI response, stores the structured information in Airtable, and sends the construction team an organized summary for review.

### Workflow

**Customer Email → Gmail Trigger → AI Analysis → JSON Parsing & Validation → Airtable → Internal AI Summary**

---

## 2. Business Problem

Construction enquiries frequently arrive as free-form emails rather than structured forms.

A customer may provide information such as:

- Name
- Email address
- Phone number
- Project type
- Location
- Budget
- Project requirements
- Desired timeline

However, this information may appear in different places and in different formats within each email.

Manually reading every enquiry and extracting the important information creates repetitive administrative work and makes it easier for important details to be missed.

The objective of this automation is to turn unstructured customer emails into structured, review-ready construction enquiries.

---

## 3. Solution

The workflow monitors a Gmail inbox and automatically processes new enquiries.

When an email arrives:

1. Gmail triggers the workflow.
2. The email sender, subject, and content are passed to an AI model.
3. AI extracts the relevant customer and project information.
4. AI classifies urgency and customer intent.
5. Missing information is identified.
6. The AI response is parsed into structured JSON.
7. The extracted information is stored in Airtable.
8. The construction team receives an HTML-formatted summary.
9. The enquiry is marked as **Needs Review**.

The workflow therefore transforms an unstructured email into an actionable business record.

---

## 4. Architecture

```text
┌──────────────────────────┐
│   Customer Email         │
│   Construction Enquiry   │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│      Gmail Trigger       │
│    Monitor Inbox         │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ AI Classify Construction │
│ Enquiry                   │
│                          │
│ Extract + Classify       │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ Parse AI Enquiry Data    │
│                          │
│ Clean + Parse JSON       │
│ Validate Email           │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│      Airtable CRM        │
│                          │
│ Store Structured Enquiry │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ Send AI Enquiry Summary  │
│                          │
│ Internal Review Email    │
└──────────────────────────┘
```

---

## 5. Workflow Components

### 5.1 Gmail Trigger

**Node:** `Gmail Trigger`

**Platform:** Gmail

**Purpose:** Detects incoming customer enquiries.

The workflow is configured to poll the Gmail inbox every minute.

When a new email is detected, the workflow receives information including the sender, subject, and email content/snippet.

This provides the entry point for processing unstructured customer enquiries.

---

### 5.2 AI Classify Construction Enquiry

**Node:** `AI Classify Construction Enquiry`

**Model:** GPT-4.1-mini

The AI receives:

- Sender
- Email subject
- Email content

The prompt instructs the model to analyse the construction enquiry and return only valid JSON.

The expected structure contains:

```json
{
  "customerName": "string",
  "customerEmail": "string",
  "customerPhone": "string",
  "serviceRequired": "string",
  "suburb": "string",
  "budget": "string",
  "urgency": "Low, Medium, or High",
  "intent": "string",
  "summary": "string",
  "missingInformation": ["string"]
}
```

### AI Responsibilities

The model performs several tasks simultaneously:

**Information extraction**

Extracts customer and project information from the email.

**Classification**

Determines the apparent urgency of the enquiry.

**Intent analysis**

Identifies what the customer is trying to accomplish.

**Summarization**

Creates a concise project summary for the construction team.

**Missing-information detection**

Identifies information that may be required before the business can proceed.

### Missing Data Handling

The prompt explicitly instructs the model not to invent information.

When information cannot be determined from the email, the model returns:

`Unknown`

This reduces the risk of fabricated customer information entering the CRM.

---

## 6. AI Output Parsing

### Node

`Parse AI Enquiry Data`

**Type:** JavaScript Code

The AI response is returned as text and must be converted into usable structured data before it can be sent to Airtable.

The parser performs several operations.

### JSON Cleanup

The node removes Markdown code fences if the AI returns JSON wrapped in:

````text
```json
...
````

````

The cleaned response is then parsed using JavaScript's `JSON.parse()`.

### Email Normalization

The parser also checks the extracted customer email address.

If the AI returns a formatted email such as a Markdown mail link, the workflow uses a regular expression to extract a plain email address.

This provides a normalization layer between the AI model and downstream business systems.

### Output

The parser returns a clean JSON object containing the structured enquiry data.

---

## 7. Airtable Storage

### Node

`Create AI Email Enquiry`

**Platform:** Airtable

**Table:** AI Email Enquiries

The structured AI output is stored in Airtable.

The record contains:

| Field | Purpose |
|---|---|
| Customer Name | Customer identity |
| Email | Customer email |
| Phone | Customer phone |
| Service Required | Requested construction service |
| Suburb | Project location |
| Budget | Customer-provided budget |
| Urgency | AI classification |
| Intent | AI interpretation of customer intent |
| AI Summary | Structured project summary |
| Missing Information | Information requiring clarification |
| Status | Current enquiry status |
| Received At | Processing timestamp |
| Original Subject | Original email subject |
| Original Email | Original email content/snippet |

New AI-processed enquiries are assigned:

**Status: Needs Review**

The original subject and email content are also retained, allowing the team to compare the AI-generated structure with the original customer message.

---

## 8. Internal AI Summary

### Node

`Send AI Enquiry Summary`

**Platform:** Gmail

Once the enquiry has been stored, the construction team receives a formatted internal email.

The summary includes:

### Customer Details

- Name
- Email
- Phone

### Project Details

- Service required
- Location
- Budget
- Urgency
- Customer intent

### AI Summary

A concise description of the enquiry generated by the AI.

### Missing Information

A list of information that should be checked or obtained before progressing the enquiry.

The email clearly identifies the enquiry as:

**Needs Review**

The team is instructed to review the original customer email before preparing a quote or contacting the customer.

---

## 9. Example Data Flow

### Input

A customer sends an unstructured email such as:

```text
Subject: Bathroom renovation enquiry

Hi,

I'm looking to renovate my bathroom in Blacktown.
The bathroom is around 3m x 2.5m and I'd like to replace
the tiles, shower, vanity and toilet.

My budget is around $25,000.

You can contact me by email.

Thanks,
Sarah
````

### AI Processing

The AI identifies information such as:

```text
Customer: Sarah
Service: Bathroom Renovation
Location: Blacktown
Budget: $25,000
Intent: Requesting a bathroom renovation
Urgency: Based on information available in the email
Missing Information: Any required details not supplied
```

### Structured Output

The result is converted into JSON and stored in Airtable.

### Business Output

The construction team receives a structured summary instead of having to manually extract every relevant detail from the original email.

---

## 10. Human-in-the-Loop Design

The system does not automatically treat the AI output as a final business decision.

Every processed enquiry is initially marked:

**Needs Review**

The construction team can review:

1. The AI-generated information
2. The original email
3. Missing information
4. Project requirements

before progressing the enquiry.

This creates a human-in-the-loop workflow where AI handles repetitive information extraction while the business retains control over customer-facing decisions.

---

## 11. Key Design Decisions

### Structured AI Output

The AI is instructed to return a fixed JSON schema rather than free-form text.

This makes the output suitable for downstream automation.

### No-Invention Rule

The AI is explicitly instructed not to invent missing information.

Unknown values are represented as:

`Unknown`

### Parsing Layer

A dedicated parsing node sits between the AI model and Airtable.

This protects the downstream workflow from common formatting issues such as Markdown code fences or incorrectly formatted email addresses.

### Original Email Retention

The original subject and email content are retained in Airtable.

This allows a human reviewer to compare the AI interpretation against the source material.

### Human Review

The workflow does not automatically approve or quote an enquiry.

AI assists the team while leaving final decisions to a human.

---

## 12. Technologies

- **n8n** — Workflow orchestration
- **Gmail** — Email trigger and internal notification
- **OpenAI GPT-4.1-mini** — AI extraction and classification
- **JavaScript** — AI response parsing and data normalization
- **Airtable** — Structured enquiry storage and review

---

## 13. Business Value

The automation can help a construction business:

- Reduce manual email processing
- Extract customer information automatically
- Standardize unstructured enquiries
- Identify missing information earlier
- Prioritize enquiries by urgency
- Understand customer intent
- Produce concise internal summaries
- Create structured CRM records
- Reduce repetitive administrative work

Most importantly, it creates a structured data layer that can be used by later automation stages.

---

## 14. Relationship to the Construction Automation Suite

This workflow can operate as a standalone email-processing system or as part of a larger construction automation platform.

Its output provides structured information that can feed downstream processes such as:

**AI Email Enquiry → Structured Project Data → Quote Preparation → Human Approval → Quote Delivery**

This makes the automation an important bridge between unstructured customer communication and automated construction workflows.

---

## 15. Current Scope

Version 1 focuses on:

**Email monitoring → AI extraction → Classification → Data normalization → CRM storage → Internal review notification**

It does not currently:

- Contact customers automatically
- Generate a quote
- Approve pricing
- Send quotations
- Perform complete lead qualification
- Automatically follow up with customers

Those functions can be implemented as subsequent automation stages.

---

## 16. Future Improvements

Potential improvements include:

- Full email body extraction rather than relying on a snippet
- Attachment processing
- PDF/document extraction
- Image analysis for plans or project photographs
- More detailed lead qualification
- Automated missing-information requests
- Duplicate enquiry detection
- Integration with the main construction CRM
- Automatic quote preparation
- Human approval workflows
- Automated follow-up

---

## 17. Project Status

**Version:** V1

**Status:** Functional prototype / demonstration system

The workflow demonstrates the use of AI to convert unstructured construction enquiries into structured, review-ready business data.
