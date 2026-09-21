# Construction Workforce Task & Progress Automation

## Overview

Construction projects often rely on employees and subcontractors to manually communicate the status of assigned work.

This creates several operational problems:

- Employees may forget to confirm when work starts.
- Managers may not know which tasks are progressing.
- Delays can go unnoticed.
- Employees may need assistance but have no structured way to report it.
- Managers may spend time manually following up with workers.
- Project progress may need to be updated manually.

This automation creates a structured workforce tracking and follow-up system using n8n, Airtable, Gmail, and automated workflow logic.

The system monitors assigned construction tasks, communicates with employees, tracks their responses, updates task status, calculates project progress, and escalates unresolved issues to management.

---

## Business Problem

A construction company may assign several tasks to different employees across a project.

For example:

| Task                      | Employee | Status  |
| ------------------------- | -------- | ------- |
| Remove existing bathroom  | James    | Pending |
| Plumbing rough in         | Ahmed    | Pending |
| Waterproofing preparation | James    | Pending |
| Wall & Floor tiling       | Mike     | Pending |
| Grouting                  | Mike     | Pending |
| Install fixtures          | Ahmed    | Pending |
| Final finishing & cleanup | James    | Pending |

Without automation, a manager has to manually check whether each employee has started their work, whether anything is delayed, and whether someone needs assistance.

This system automates that process.

---

## What the Automation Does

The system provides:

1. Automated work reminders
2. Employee status tracking
3. Task status updates
4. Project progress tracking
5. Delay detection and manager notification
6. Employee assistance requests
7. Automatic follow-up for non-responsive employees
8. Manager escalation for unresolved tasks
9. Duplicate notification prevention
10. Timestamp-based activity tracking

---

# System Architecture

The solution is divided into three n8n workflows.

### Workflow 1 — Daily Work Reminders

Checks scheduled construction tasks and sends employees a reminder when work is due.

```text
Manual Trigger
      ↓
Find Today's Tasks
      ↓
Tasks Found?
      ↓
Send Work Reminder
      ↓
Mark Reminder Sent
```

The system records when the reminder was sent to prevent duplicate reminders.

Workflow 2 — Employee Status Tracking

Employees respond to the work reminder using simple structured commands.

Gmail Trigger
↓
Parse Employee Response
↓
Find Task
↓
Process Employee Action

Supported employee commands:

STARTED tsk-002
COMPLETED tsk-002
DELAYED tsk-004 waiting for materials
NEEDS HELP tsk-006 fixture doesn't fit

The workflow interprets the response and updates Airtable accordingly.

Workflow 3 — Employee Escalation Monitor

The system identifies tasks where an employee has received a reminder and follow-up but has still not provided a STARTED confirmation.

Manual Trigger
↓
Find Tasks Needing Escalation
↓
Escalation Due?
↓
Escalate to Manager
↓
Mark Escalation Sent

Duplicate escalation notifications are prevented using an Airtable tracking field.

Employee Status Flow

The system supports the following task lifecycle:

Pending
↓
Reminder Sent
↓
In Progress
↓
Completed

Exception paths:

Reminder Sent
├── DELAYED → Delayed → Manager Alert
│
└── NEEDS HELP → Needs Attention → Manager Alert

Non-response path:

Reminder Sent
↓
No response
↓
Follow-Up Sent
↓
Still no response
↓
Manager Escalation
Airtable Data Model

The Tasks table contains the operational state of each assigned task.

Field Purpose
Task ID Unique task identifier
Project ID Links task to project
Task Name Description of assigned work
Assigned Employee Employee responsible
Employee Email Employee notification address
Scheduled Date Planned task date/time
Status Current task status
Reminder Sent Prevents duplicate reminders
Reminder Sent At Reminder timestamp
Started At Employee start timestamp
Completed At Completion timestamp
Employee Update Delay/help information
Follow-Up Sent Prevents duplicate follow-ups
Follow-Up Sent At Follow-up timestamp
Escalation Sent Prevents duplicate escalations
Escalation Sent At Escalation timestamp
Task Status Values
Pending
Reminder Sent
In Progress
Completed
Delayed
Needs Attention
Automation Logic

1. Work Reminder

The reminder workflow searches for tasks where:

Scheduled Date = Today
Status != Completed
Reminder Sent = false

If a matching task is found, the employee receives a work reminder.

The system then records:

Reminder Sent = true
Reminder Sent At = current timestamp 2. STARTED

Example:

STARTED tsk-002

The system:

Finds the matching task
Updates the status to In Progress
Records the start timestamp 3. COMPLETED

Example:

COMPLETED tsk-002

The system:

Updates the task to Completed
Records the completion timestamp
Updates project progress

Example:

Completed Tasks: 1 / 7
Progress: 14% 4. DELAYED

Example:

DELAYED tsk-004 waiting for materials

The system:

Updates the task status to Delayed
Stores the employee's explanation
Sends a manager notification 5. NEEDS HELP

Example:

NEEDS HELP tsk-006 fixture doesn't fit

The system:

Updates the task status to Needs Attention
Stores the employee's message
Sends an assistance notification to management 6. Employee Follow-Up

If a task has:

Reminder Sent = true
Status = Reminder Sent
Follow-Up Sent = false

and sufficient time has passed since the reminder, the system sends the employee a follow-up request.

The system then records:

Follow-Up Sent = true
Follow-Up Sent At = current timestamp 7. Manager Escalation

If the employee still has not responded after the follow-up period, the system escalates the task to management.

The manager receives:

Project
Task
Task ID
Assigned employee
Scheduled date
Current status
Recommended action

The system then records:

Escalation Sent = true
Escalation Sent At = current timestamp

This prevents the same task from generating repeated escalation emails.

Technologies
n8n — Workflow automation and orchestration
Airtable — Task and project data management
Gmail — Employee and manager communication
JavaScript — Data parsing and workflow logic
Date/time logic — Follow-up and escalation timing
Example Scenario

A construction company has a bathroom renovation project with seven assigned tasks.

An employee receives:

Work Reminder — Plumbing Rough In

The employee replies:

STARTED tsk-002

The system automatically updates Airtable:

Status: In Progress
Started At: [timestamp]

Later, the employee replies:

COMPLETED tsk-002

The system updates:

Status: Completed
Completed At: [timestamp]

and recalculates project progress:

Completed Tasks: 1 / 7
Progress: 14%

If another employee instead replies:

DELAYED tsk-004 waiting for materials

the system records the delay and immediately notifies management.

If an employee doesn't respond at all, the system automatically sends a follow-up and eventually escalates the unresolved task to management.

Business Value

This automation helps construction businesses reduce manual workforce coordination by providing a structured communication and tracking layer around assigned work.

Potential benefits include:

Less manual employee follow-up
Faster visibility into delays
Earlier identification of operational problems
Better project progress visibility
Consistent workforce communication
Automatic management escalation
Centralized task history
Reduced risk of forgotten updates
Demo

The recommended demonstration covers two scenarios.

Scenario 1 — Normal Task Completion
Task Assigned
↓
Work Reminder
↓
Employee: STARTED
↓
Task → In Progress
↓
Employee: COMPLETED
↓
Task → Completed
↓
Project Progress Updated
Scenario 2 — Exception & Escalation
Task Assigned
↓
Work Reminder
↓
No Employee Response
↓
Follow-Up
↓
Still No Response
↓
Manager Escalation

Additional exception examples:

DELAYED tsk-004 waiting for materials

and

NEEDS HELP tsk-006 fixture doesn't fit
Current Version

Version: V1

The current implementation is designed as a demonstration-ready workforce coordination system.

Manual triggers are intentionally retained during development and demonstration. Production deployments can replace these with client-specific scheduling and operational rules.

Future Improvements

Potential production enhancements include:

SMS or WhatsApp employee communication
Mobile-friendly employee status interface
Manager dashboard
Real-time project progress dashboard
Automatic overdue task detection
Employee response analytics
Crew-level reporting
Integration with construction management software
Client-specific escalation rules
Calendar integration
Automated daily management summaries
Conclusion

Construction Workforce Task & Progress Automation demonstrates how AI-assisted and rule-based workflow automation can reduce manual project coordination.

The system connects employee communication, task management, project progress tracking, exception handling, and management escalation into a single automated workflow.
