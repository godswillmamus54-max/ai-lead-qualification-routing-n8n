# AI Lead Qualification & Routing System
## n8n Installation Guide

---

## Overview

The AI Lead Qualification & Routing System automatically captures, validates, qualifies, routes, and stores incoming business leads.

The workflow:

1. Receives leads through a webhook
2. Validates required information
3. Checks for duplicate leads
4. Uses AI to qualify the lead
5. Assigns a score from 0–100
6. Classifies the lead as Hot, Warm, or Cold
7. Sends Hot Lead alerts to Slack
8. Stores qualified leads in Google Sheets
9. Handles duplicate submissions
10. Handles invalid submissions

---

# Requirements

Before importing the workflow, you need:

- An n8n instance
- An OpenAI API account and credential
- A Slack workspace
- A Google account
- A Google Sheets document

---

# 1. Import the Workflow

1. Open your n8n instance.
2. Import the file:

   `AI-Lead-Qualification-Routing-System.json`

3. Open the imported workflow.
4. Save the workflow.

---

# 2. Configure OpenAI

Open the:

`AI Qualification`

node.

Open the connected Chat Model and select or create your OpenAI credential.

Use your own OpenAI API credential.

Do not share API keys with the workflow seller or other users.

---

# 3. Configure Slack

Open:

`Notify Hot Lead — Slack`

Create or select the Slack credential for your workspace.

Choose the channel where Hot Lead notifications should be delivered.

Example:

`#lead-alerts`

The workflow sends Slack notifications only for leads classified as Hot.

Warm and Cold leads continue through the workflow and are stored in Google Sheets without triggering the Hot Lead Slack alert.

---

# 4. Create the Google Sheet

Create a Google Sheets document.

Create a sheet/tab named:

`Leads`

The first row must contain the following column headers:

| Column |
|---|
| lead_id |
| received_at |
| name |
| email |
| company |
| phone |
| message |
| budget |
| source |
| is_duplicate |
| score |
| temperature |
| qualification_reason |
| recommended_action |
| key_need |

Connect your Google account to:

`Save Lead — Google Sheets`

Select the correct Google Sheets document and the `Leads` sheet.

The same `Leads` sheet is used for both duplicate checking and storing qualified leads.

---

# 5. Configure the Webhook

The workflow receives incoming leads through an HTTP POST webhook.

The expected JSON structure is:

```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "company": "Example Ltd",
  "phone": "+123456789",
  "message": "We need an automation system.",
  "budget": "5000",
  "source": "website"
}

The following fields are expected:

name
email
company
phone
message
budget
source

The required fields for qualification are:

email
message

After importing and configuring the workflow, activate it and copy the Production Webhook URL.

Use the Production Webhook URL in your:

Website
Lead form
CRM
Marketing system
Other lead source

Do not use the n8n Test Webhook URL for a live production integration.

6. Lead Qualification

The AI evaluates the information provided by each lead.

The workflow assigns a score from 0 to 100 and classifies the lead into one of three categories.

Hot

Score:

80–100

A Hot lead should have strong evidence of buying intent.

Examples include:

A specific business problem
A clear request to purchase or implement a service
A meaningful budget
A specific implementation requirement
A timeline or urgency
Multiple strong buying signals

Hot leads trigger a Slack notification.

Warm

Score:

50–79

A Warm lead has legitimate potential but does not provide enough evidence to qualify as Hot.

Examples include:

A genuine business-related need
Interest in a possible solution
Some useful qualification information
Missing budget, timeline, urgency, or specific requirements

Warm leads are stored in Google Sheets without triggering the Hot Lead Slack notification.

Cold

Score:

0–49

A Cold lead has little or insufficient evidence of genuine buying intent.

Examples include:

Vague inquiries
General information requests
Exploratory messages
Unclear requirements
No identifiable business need
Insufficient qualification information

Cold leads are stored in Google Sheets without triggering the Hot Lead Slack notification.

7. Duplicate Protection

The workflow checks the incoming lead's email address against the Leads Google Sheet.

If the email already exists:

The lead is identified as a duplicate.
AI qualification is skipped.
A duplicate status is returned.
A second lead record is not created.
The lead does not trigger another Hot Lead notification.

This prevents repeated submissions from unnecessarily consuming AI resources or creating duplicate records.

8. Invalid Lead Protection

The workflow requires the following fields:

Email
Message

If either required field is missing:

The lead is rejected before AI qualification.
The AI is not called.
Duplicate checking is skipped.
Slack notification is not triggered.
The lead is not saved as a qualified lead.

The workflow returns an invalid status and identifies the missing required field.

Example:

{
  "status": "invalid",
  "message": "Lead submission is missing required fields.",
  "missing_fields": [
    "email"
  ]
}
9. Testing

Postman or another API testing application can be used to test the webhook.

Use:

Method:

POST

Body:

raw → JSON

Content-Type:

application/json

Example test payload:

{
  "name": "Sarah Williams",
  "email": "sarah@example.com",
  "company": "Example Technologies",
  "phone": "+14155550199",
  "message": "We need an AI lead qualification system and would like to discuss implementation.",
  "budget": "7500",
  "source": "website"
}

Verify that:

The webhook receives the request.
The lead passes validation.
Duplicate checking succeeds.
AI qualification runs.
The lead receives a score.
The lead receives a Hot, Warm, or Cold classification.
Hot leads trigger the Slack notification.
The lead is saved to Google Sheets.
10. Testing Duplicate Leads

To test duplicate protection:

Submit a lead with a new email address.
Confirm that the lead is saved to the Leads sheet.
Submit the same email address again.
Confirm that the workflow identifies the submission as a duplicate.
Confirm that AI qualification is skipped.
Confirm that another database record is not created.
11. Testing Invalid Leads

To test invalid lead protection, submit a payload with a missing email or message.

Example:

{
  "name": "Invalid Test Lead",
  "email": "",
  "company": "Test Company",
  "phone": "+1234567890",
  "message": "We need an automation system.",
  "budget": "1000",
  "source": "website"
}

The workflow should return an invalid status and identify email as a missing required field.

12. Troubleshooting
Slack notification fails

Check:

Slack credential
Selected Slack channel
Slack workspace
Slack app permissions
Google Sheets fails

Check:

Google credential
Spreadsheet selection
Sheet name
Column headers
Google Sheets permissions

Make sure the sheet is named:

Leads

and that the required headers exist in the first row.

Duplicate detection fails

Check that:

The duplicate lookup uses the Leads sheet.
The lookup uses the email column.
The submitted email matches the email stored in the sheet.
AI qualification fails

Check:

OpenAI credential
AI model configuration
AI Qualification node
Structured Output Parser
AI qualification prompt
Webhook receives no data

Check:

HTTP method is POST
Content-Type is application/json
Correct webhook URL is being used
Workflow is activated when using the Production Webhook URL
The sending application is sending the expected JSON structure
13. Security

Never share or expose:

OpenAI API keys
Slack credentials
Google credentials
n8n passwords
Authentication secrets
Private webhook credentials

Each user or business should connect their own credentials after importing the workflow.

The workflow template should not contain the seller's private API keys or account credentials.

14. Workflow Architecture

The complete workflow follows this structure:

Lead Source
    ↓
Webhook
    ↓
Normalize Lead
    ↓
Required Fields Validation
    ↓
Duplicate Check
    ↓
Duplicate?
    ├── TRUE → Handle Duplicate Lead
    │
    └── FALSE
          ↓
    Prepare New Lead
          ↓
    AI Qualification
          ↓
    Validate AI Qualification
          ↓
    Route Lead Temperature
       ├── HOT
       │    ↓
       │ Format Hot Lead
       │    ↓
       │ Slack Notification
       │
       ├── WARM
       │    ↓
       │ Format Warm Lead
       │
       └── COLD
            ↓
         Format Cold Lead

HOT / WARM / COLD
        ↓
Merge Qualified Leads
        ↓
Save Lead — Google Sheets
15. Lead Data Stored

Each qualified lead can contain:

Lead ID
Received timestamp
Name
Email
Company
Phone
Message
Budget
Source
Duplicate status
AI score
Temperature
Qualification reason
Recommended action
Key business need
16. Customization

The workflow can be extended to support additional business systems and actions.

Possible extensions include:

CRM integration
Email notifications
Microsoft Teams notifications
Discord notifications
Additional Slack channels
Lead assignment
Automated follow-up
Calendar scheduling
Database storage
Customer relationship management systems
Additional AI qualification criteria
Custom scoring rules
Industry-specific qualification logic

Custom integrations may require additional configuration or development.

17. Production Deployment

Before using the workflow with real customers or live leads:

Import the workflow.
Configure all credentials.
Configure the Google Sheets document.
Configure the Slack channel.
Test the webhook.
Test a valid lead.
Test a duplicate lead.
Test an invalid lead.
Confirm Slack notifications.
Confirm Google Sheets records.
Activate the workflow.
Copy the Production Webhook URL.
Connect the Production Webhook URL to the lead source.

Do not use the Test Webhook URL for live production traffic.

18. Support

The workflow provides a reusable foundation for automated AI lead qualification, routing, notification, and lead management.

Client-specific integrations, CRM connections, additional notifications, custom scoring rules, or other modifications can be added separately.