# Construction Lead Capture & CRM Automation

## 1. Overview

The Construction Lead Capture & CRM Automation is an automated lead intake system designed for construction and home-improvement businesses.

The workflow receives a new customer enquiry submitted through a Tally form, transforms the incoming form data into a structured format, creates a lead record in an Airtable-based CRM, notifies the construction company by email, and sends an automatic confirmation email to the customer.

### Workflow

**Tally Form → n8n Webhook → Data Formatting → Airtable CRM → Internal Notification → Customer Confirmation**

---

## 2. Business Problem

Construction companies can receive enquiries containing multiple pieces of information, including:

- Customer contact details
- Required service
- Project description
- Location
- Budget
- Preferred contact method
- Lead source

Handling these enquiries manually can result in:

- Repetitive data entry
- Delayed internal notifications
- Inconsistent CRM records
- Delayed customer responses
- Leads being overlooked or incorrectly recorded

The objective of this automation is to create a consistent intake process from the moment a customer submits an enquiry.

---

## 3. Solution

The automation creates a centralized workflow that automatically processes every new enquiry.

When a customer submits the form:

1. Tally sends the submission to an n8n webhook.
2. n8n extracts the relevant form fields.
3. The data is normalized into a consistent lead structure.
4. A new lead record is created in Airtable.
5. The construction company receives an internal notification.
6. The customer receives an automated confirmation.

This creates a basic automated lead-to-CRM pipeline without requiring manual data entry.

---

## 4. Architecture

```text
┌─────────────────┐
│   Tally Form    │
│ Customer Enquiry│
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  n8n Webhook    │
│ POST /construction-lead
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Format Tally    │
│ Lead            │
│ Data extraction │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Prepare Lead    │
│ Data            │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Airtable CRM    │
│ Create Lead     │
│ Status: New     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Internal Email  │
│ Construction Co.│
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Customer Email  │
│ Confirmation    │
└─────────────────┘
```

---

## 5. Workflow Components

### 5.1 Webhook

**Node:** `Webhook`

**Purpose:** Receives new lead submissions from the external form.

**Method:** POST

**Endpoint:** `/construction-lead`

The webhook acts as the entry point for the automation.

---

### 5.2 Format Tally Lead

**Node:** `Format Tally Lead`

**Type:** Code

The incoming Tally payload contains the submitted form fields inside the webhook request.

The Code node searches for fields by their labels and extracts their values.

The incoming submission is transformed into a standardized lead object containing:

```text
leadId
fullName
email
phone
serviceRequired
projectDescription
suburb
budget
preferredContact
leadSource
receivedAt
```

This creates a predictable structure for the rest of the workflow.

---

### 5.3 Prepare Lead Data

**Node:** `Prepare Lead Data`

**Type:** Set

This node explicitly maps the normalized lead fields into the structure required by the downstream CRM and notification steps.

This provides a clean separation between incoming form data and the data used by the business workflow.

---

### 5.4 Create Airtable Record

**Node:** `Create a record`

**Platform:** Airtable

**Base:** Construction CRM

**Table:** Leads

A new CRM record is created for every enquiry.

The following information is stored:

| Field               | Description                         |
| ------------------- | ----------------------------------- |
| Lead ID             | Unique submission identifier        |
| Full Name           | Customer name                       |
| Email               | Customer email                      |
| Phone               | Customer phone number               |
| Suburb              | Project location                    |
| Service Required    | Requested service                   |
| Project Description | Customer's project requirements     |
| Budget              | Customer-provided budget            |
| Preferred Contact   | Preferred communication method      |
| Lead Source         | How the customer found the business |
| Received At         | Submission timestamp                |
| Status              | Initial lead status                 |

New records are assigned the status:

**New**

The CRM supports the following lead lifecycle:

```text
New → Contacted → Quote Requested → Quote Sent → Won / Lost
```

---

### 5.5 Notify Construction Company

**Node:** `Notify Construction Company`

**Platform:** Gmail

After the CRM record is created, the construction company receives an automated HTML-formatted notification.

The notification includes:

- Customer information
- Contact details
- Requested service
- Location
- Budget
- Preferred contact method
- Project description
- Current lead status

The purpose is to ensure that the business is immediately notified when a new enquiry arrives.

---

### 5.6 Customer Confirmation Email

**Node:** `Customer Confirmation Email`

**Platform:** Gmail

The customer receives an automated confirmation immediately after the lead has been processed.

The confirmation includes:

- Customer name
- Requested service
- Location
- Preferred contact method
- Confirmation that the enquiry was received
- Expected next step

This provides an immediate customer response without requiring manual intervention.

---

## 6. Data Flow

### Input

The workflow expects a lead submission containing:

```json
{
  "fullName": "Customer Name",
  "email": "customer@example.com",
  "phone": "+61XXXXXXXXX",
  "serviceRequired": "Bathroom Renovation",
  "projectDescription": "Renovate existing bathroom",
  "suburb": "Blacktown",
  "budget": "$25,000",
  "preferredContact": "Email",
  "leadSource": "Google"
}
```

### Processing

The submission is:

1. Received through the webhook.
2. Parsed from the Tally payload.
3. Normalized into a standard lead object.
4. Stored in Airtable.
5. Used to generate internal and external email notifications.

### Output

The workflow produces:

1. A new lead record in the CRM.
2. An internal lead notification.
3. A customer confirmation email.

---

## 7. Technologies

- **n8n** — Workflow orchestration and automation
- **Tally** — Customer enquiry form
- **Airtable** — CRM and lead database
- **Gmail** — Internal and customer email communication
- **JavaScript** — Lead data extraction and transformation

---

## 8. Key Design Decisions

### Structured Data Transformation

Rather than passing the raw form submission through the workflow, the Tally payload is converted into a standardized structure.

This makes the workflow easier to maintain and allows downstream nodes to work with predictable field names.

### CRM Lifecycle

The Airtable CRM uses a defined lead status pipeline:

```text
New
Contacted
Quote Requested
Quote Sent
Won
Lost
```

This provides a foundation for future automations.

### Automated Customer Response

The customer receives confirmation immediately after submission, reducing the delay between enquiry and acknowledgement.

### Human-in-the-Loop

This automation handles lead intake and communication but does not make decisions about whether a lead should be accepted, quoted, or won.

Those business decisions remain with the construction company.

---

## 9. Business Value

The automation can help a construction business:

- Reduce manual lead entry
- Centralize incoming enquiries
- Standardize lead information
- Notify staff immediately
- Provide faster customer acknowledgement
- Establish a structured CRM pipeline
- Create a foundation for downstream quote and follow-up automation

---

## 10. Current Scope

The current version focuses on:

**Lead capture → CRM creation → Internal notification → Customer acknowledgement**

It does not currently perform AI lead qualification, quote generation, automated follow-up, or automated customer conversion.

Those capabilities can be implemented as additional stages in the wider construction automation system.

---

## 11. Future Improvements

Potential extensions include:

- AI-powered lead qualification
- Automatic lead scoring
- Duplicate lead detection
- Automated follow-up reminders
- AI extraction from email enquiries
- Quote preparation
- Human approval workflows
- Automated quote delivery
- Customer follow-up sequences
- Lead analytics and reporting

---

## 12. Project Status

**Version:** V1

**Status:** Functional prototype / demonstration system

The automation has been developed as part of a broader construction automation suite demonstrating how AI and workflow automation can streamline the customer enquiry-to-quote process.
