# Construction Workflow 5 — Review & Follow-Up

## Overview

The **Review & Follow-Up** workflow automates post-project customer follow-up for the construction automation system.

Once a project reaches the appropriate stage, the workflow searches Airtable for the relevant project/customer record, sends a review/follow-up email through Gmail, and updates the Airtable record to indicate that the follow-up has been sent.

This workflow is designed to demonstrate how a construction business can maintain consistent customer communication after project completion without relying on manual follow-up.

---

## Workflow Purpose

The workflow handles the post-project review and follow-up process:

1. Manually start the workflow.
2. Search Airtable for the project/customer record that requires follow-up.
3. Send a follow-up email through Gmail.
4. Update the Airtable record to confirm that the follow-up was sent.

---

## Workflow Architecture

```text
Manual Trigger
      ↓
Airtable — Search Records
      ↓
Gmail — Send Message
      ↓
Airtable — Update Record
```

---

## Nodes

### 1. Manual Trigger

**Purpose:**
Starts the workflow manually.

**Why manual:**
The current demonstration workflow intentionally uses a manual trigger so the business owner can initiate the review/follow-up process when required.

**Input:**
No external input is required.

---

### 2. Airtable — Search Records

**Purpose:**
Searches the Construction CRM for the project/customer record that needs a review or follow-up.

The Airtable record provides the information required by the following steps, such as:

- Customer name
- Customer email
- Project information
- Project status
- Review/follow-up status
- Airtable record ID

**Output:**
The matching Airtable record is passed to the Gmail node.

---

### 3. Gmail — Send Message

**Purpose:**
Sends the customer a post-project review/follow-up email.

The email can be used to:

- Thank the customer for choosing the company
- Ask about their experience
- Request a review
- Maintain the customer relationship
- Create an opportunity for future work or referrals

The recipient and relevant project/customer information are populated from the Airtable record.

**Output:**
Confirmation that the Gmail message was sent successfully.

---

### 4. Airtable — Update Record

**Purpose:**
Updates the original Airtable record after the follow-up email has been sent.

This provides a record of the communication and prevents the CRM from treating the follow-up as still pending.

Typical information updated includes:

- Review/follow-up sent status
- Review/follow-up date
- Related workflow status

---

## Data Flow

The workflow passes customer and project information through the automation:

```text
Airtable Customer/Project Record
            ↓
       Customer Data
            ↓
       Gmail Email
            ↓
     Send Confirmation
            ↓
      Airtable Update
```

The Airtable record acts as the source of customer/project information and the final system of record for the follow-up status.

---

## Example Use Case

A construction company has completed a customer's project.

Instead of relying on a staff member to remember to contact the customer, the business can initiate this workflow.

The workflow:

1. Finds the relevant customer/project record.
2. Retrieves the customer's contact information.
3. Sends a personalized follow-up email.
4. Records that the follow-up has been completed.

For example:

```text
Customer: Sarah Mitchell
Project: Residential Renovation
Location: Blacktown, NSW
Project Status: Completed
Review Sent: Yes
```

This creates a simple but trackable post-project communication process.

---

## Requirements

The workflow requires:

- n8n
- Airtable
- Gmail
- A Construction CRM Airtable base/table containing the required customer/project information
- Valid Airtable credentials
- Valid Gmail credentials

---

## Configuration

### Airtable

Configure the Airtable nodes to use the Construction CRM table containing the relevant project and customer records.

Ensure the search criteria and update fields correspond to the current Airtable schema.

### Gmail

Connect the Gmail credential used by the construction automation system.

Configure the email recipient, subject, and message using values from the Airtable record.

### Airtable Update

The update node should target the same Airtable record returned by the search step.

Ensure the correct Airtable Record ID is passed into the update operation.

---

## Error Considerations

Potential failure points include:

### No matching Airtable record

If the search does not return a matching record, the Gmail step will not have the required customer information.

### Missing customer email

If the Airtable record does not contain a valid email address, the Gmail message cannot be sent.

### Gmail authentication failure

The workflow requires an active and authorized Gmail credential.

### Airtable update failure

The email may successfully send while the final Airtable update fails. In this situation, the customer may receive the message but the CRM may not reflect that the follow-up was completed.

---

## Business Value

This workflow helps a construction company create a repeatable post-project communication process.

It provides:

- Consistent customer follow-up
- Reduced administrative work
- Centralized communication tracking
- Better visibility of completed follow-ups
- A foundation for automated review collection and customer retention

---

## Future Improvements

The workflow can be extended to include:

- Automated scheduling after project completion
- Review links for Google or other review platforms
- Follow-up reminders if the customer does not respond
- AI-generated personalized follow-up messages
- Customer response classification
- Automatic CRM updates based on customer responses
- Referral-request automation
- Multi-stage customer retention sequences

---

## Workflow Summary

**Trigger:** Manual

**Input:** Construction customer/project record in Airtable

**Process:**

- Search for the relevant record
- Send review/follow-up email
- Update the Airtable record

**Output:**
Customer receives a follow-up message and the CRM records that the follow-up has been sent.

**Workflow Type:** Post-project customer communication / review follow-up

**Status:** Completed
