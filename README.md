# AI Lead Qualification & Routing System

An AI-powered n8n automation that captures incoming business leads, validates submissions, detects duplicates, qualifies leads with AI, assigns a score, routes leads by temperature, alerts sales teams to high-value opportunities, and stores lead data in Google Sheets.

**Author:** Godswill Ogheneochuko

**Portfolio:** https://godswillai.dev/

**GitHub:** https://github.com/godswillmamus54-max

## Overview

Manual lead qualification can consume valuable sales-team time and cause high-value opportunities to be missed.

This workflow automates the initial qualification process so sales teams can focus their attention on leads that require human follow-up.

The system automatically:

- Receives leads through a webhook
- Normalizes incoming lead data
- Validates required fields
- Checks for duplicate leads
- Uses AI to evaluate lead quality
- Assigns a score from 0–100
- Classifies leads as Hot, Warm, or Cold
- Routes leads based on qualification
- Sends Hot Lead alerts to Slack
- Stores processed qualified leads in Google Sheets
- Handles duplicate submissions
- Handles invalid submissions

---

## Workflow Architecture

```text
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
AI Lead Scoring

The AI evaluates each incoming lead using only the information provided and assigns a score from 0 to 100.

🔥 Hot — 80–100

Strong evidence of buying intent.

Potential signals include:

A specific business problem
A clear request to purchase or implement a solution
A meaningful budget
Specific implementation requirements
A defined timeline or urgency
Multiple strong buying signals

Action: Hot leads trigger a Slack notification for immediate sales attention.

🟡 Warm — 50–79

A legitimate potential lead with some business interest, but without enough evidence to qualify as Hot.

Potential signals include:

A genuine business-related need
Interest in a potential solution
Some useful qualification information
Missing budget, urgency, timeline, or specific requirements

Action: Warm leads continue through the workflow and are stored for follow-up.

🔵 Cold — 0–49

Limited or insufficient evidence of genuine buying intent.

Examples include:

Vague inquiries
General information requests
Exploratory questions
Unclear requirements
No identifiable business need
Insufficient qualification information

Action: Cold leads are stored without triggering the Hot Lead Slack alert.

Key Features
Webhook Lead Capture

The system can receive leads from:

Websites
Lead forms
CRMs
Marketing systems
Custom applications
Other HTTP-compatible sources
Expected Input
{
  "name": "John Doe",
  "email": "john@example.com",
  "company": "Example Ltd",
  "phone": "+123456789",
  "message": "We need an automation system.",
  "budget": "5000",
  "source": "website"
}
Lead Validation

Required fields:

email
message

Invalid submissions are stopped before AI qualification.

Duplicate Protection

The workflow checks the incoming email address against the Leads Google Sheets database.

If the email already exists:

The lead is identified as a duplicate
AI qualification is skipped
A duplicate status is returned
A second lead record is not created
A second Hot Lead notification is not triggered

This helps reduce:

Duplicate records
Unnecessary AI processing
Repeated notifications
Unnecessary API usage
AI Qualification

The AI returns:

Score
Temperature
Qualification reason
Recommended action
Key business need
AI Output Validation

The workflow validates the AI result before continuing.

The score must be:

An integer
Between 0 and 100

The temperature must be:

Hot
Warm
Cold

The temperature must also correspond to the score range.

Slack Alerts

Hot leads are automatically sent to a configured Slack channel with the information required for sales follow-up.

Google Sheets Lead Database

Processed qualified leads are stored in a structured Google Sheets database.

Stored information includes:

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
Example Use Case

A website visitor submits:

"We need an AI-powered lead qualification system for our sales team. We need to implement it this month and would like to discuss the requirements."

The workflow:

Receives the lead
Normalizes the information
Validates the submission
Checks whether the email already exists
Sends the new lead to the AI qualification engine
Generates a qualification score
Classifies the lead as Hot, Warm, or Cold
Validates the AI result
Sends a Slack alert if the lead is Hot
Stores the lead and qualification data in Google Sheets

The sales team can then prioritize follow-up without manually reviewing every incoming inquiry.

Example AI Output
{
  "score": 85,
  "temperature": "Hot",
  "reason": "The lead has a specific business need, a meaningful budget, and clear implementation intent.",
  "recommended_action": "Prioritize follow-up and schedule a discovery call.",
  "key_need": "AI-powered lead qualification and routing"
}
Who Can Use This?

This automation can be adapted for:

SaaS companies
Marketing agencies
Lead-generation agencies
Consulting businesses
Real estate companies
Local service businesses
B2B sales teams
Professional services
Online businesses
Startups
Technology Stack
n8n
OpenAI
Slack
Google Sheets
Webhooks
JavaScript
AI structured output
Customization Options

The workflow can be extended to support additional systems and business requirements.

Possible customizations include:

CRM integration
Email notifications
Microsoft Teams
Discord
Additional Slack channels
Lead assignment
Automated follow-up
Calendar scheduling
Database storage
Custom scoring rules
Industry-specific qualification criteria
Additional AI analysis
Custom lead-routing logic

Custom integrations may require additional configuration or development.

Requirements

To use this workflow, you need:

An n8n instance
OpenAI API access
A Slack workspace
A Google account
Google Sheets

Each user should connect their own credentials.

Installation

Import:

AI-Lead-Qualification-Routing-System.json

Then follow:

INSTALLATION-GUIDE.md

The installation guide explains how to configure:

OpenAI credentials
Slack credentials
Google Sheets
Webhook
Testing
Production deployment

Security

### Private Files

The n8n workflow JSON is intentionally excluded from the public GitHub repository.

The commercial Payhip package is also kept outside Git tracking.

These files remain available locally for deployment and product distribution.

This workflow template should not contain private credentials.

Users must configure their own:

OpenAI API credentials
Slack credentials
Google credentials
n8n authentication
Webhook authentication where applicable

Never publish:

API keys
Passwords
Private webhook secrets
Authentication tokens
Other sensitive credentials

Project Structure

AI-Lead-Qualification-Routing-System/
│
├── .gitignore
├── INSTALLATION-GUIDE.md
├── LICENSE.txt
├── README.md
├── SAMPLE-PAYLOADS.json
└── AI-Lead-Qualification-Routing-System.json   # private/local

The workflow has been tested against the following scenarios:

Test	Result
Valid lead processing	✅
Hot lead qualification	✅
Warm lead qualification	✅
Cold lead qualification	✅
Duplicate lead detection	✅
Duplicate handling	✅
Invalid lead detection	✅
AI structured output	✅
AI qualification validation	✅
Temperature routing	✅
Hot Slack notification	✅
Google Sheets storage	✅
Production Deployment

Before connecting the workflow to live lead sources:

Import the workflow
Configure the required credentials
Configure the Google Sheets database
Configure the Slack notification channel
Test the webhook
Test a valid lead
Test a duplicate lead
Test an invalid lead
Confirm Slack notifications
Confirm Google Sheets records
Activate the workflow
Copy the Production Webhook URL
Connect the Production Webhook URL to the lead source

Do not use the n8n Test Webhook URL for live production traffic.

Support & Custom Development

This workflow provides a reusable foundation for automated AI lead qualification and routing.

Additional integrations, custom business logic, CRM connections, notification systems, and industry-specific qualification rules can be developed separately.

License

This project is provided as a reusable automation template.

Commercial redistribution, modification, or resale should follow the licensing terms established by the seller or project owner.