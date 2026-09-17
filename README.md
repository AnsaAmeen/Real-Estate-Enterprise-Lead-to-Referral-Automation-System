# Real Estate Enterprise Lead-to-Referral Automation System

An end-to-end n8n automation system built for real estate agencies. It manages the complete lead lifecycle, from first contact through closing and referral, using AI-based lead scoring, territory-based agent routing, and fully automated follow-up sequences.

---

## Overview

- **50 connected nodes** covering intake, scoring, routing, nurturing, reporting, and error handling
- **Multi-channel lead capture** from missed calls, website forms, and WhatsApp
- **AI-driven decision making**, not just AI-generated replies
- **Built for real agency operations**, not a demo-only proof of concept

---

## Core Features

### 1. Multi-Channel Lead Intake
- Captures leads simultaneously from missed calls, website form submissions, and WhatsApp messages
- Normalizes all three sources into a single consistent lead format before processing

### 2. Urgency Detection and Escalation
- Every incoming message is checked for frustration or urgency
- Urgent messages bypass standard scoring entirely and go straight to a human, regardless of lead score

### 3. AI Lead Scoring (Hot / Warm / Cold)
- Uses structured function calling, not free-text guessing, to output a reliable score
- References real property inventory data and prior conversation history before responding
- Extracts intent, budget signals, and timeline directly from the lead's own words

### 4. Territory-Based Agent Routing
- Hot leads are automatically matched to the correct agent based on their stated area of interest
- No shared inbox, no manual triage, the right agent is notified directly

### 5. Automated Nurture Sequences
- Warm leads receive a spaced, multi-day follow-up sequence with no manual effort required
- Cold leads are logged for long-term follow-up instead of being chased prematurely

### 6. Appointment Reminders and No-Show Handling
- Automatic reminder sent the evening before a scheduled viewing
- Automatic detection of no-shows the day after, triggering a rebooking sequence

### 7. Post-Viewing Feedback and Offer Stage Routing
- Automated feedback request sent after every completed viewing
- Interested leads are escalated directly to a human for the offer stage
- Uninterested leads are moved into a longer-term nurture list, not dropped

### 8. Closing and Referral Automation
- Triggered automatically when an agent marks a deal "Closed" in the CRM
- Sends a thank-you message immediately, followed by an automated referral request three weeks later

### 9. Weekly Reporting
- Every Monday, an AI-generated summary of the week's lead activity is delivered automatically
- No manual report building required

### 10. Dedicated Error Handling
- A separate error-handling path monitors the entire system
- The team is alerted immediately if any node fails, instead of leads silently disappearing

---

## Current Status: Testing Phase

This system is fully built and operational in n8n. It is currently in a **testing phase**, with one limitation worth stating transparently:

- Twilio SMS delivery has restrictions in the developer's current region (Pakistan)
- To fully test workflow logic and timing during development, several outbound messaging steps are temporarily routed through Slack instead of live SMS
- In production, these same steps are designed to send real SMS or WhatsApp messages directly to leads. Reverting to live messaging is a configuration change, not a redesign, since the required node types already exist in the workflow

This is a standard development practice and does not reflect a limitation in the system's design.

---

## Repository Structure

```
real-estate-enterprise-system/
├── README.md
├── n8n/
│   └── Real Estate - Enterprise Lead-to-Referral System (Production Ready).json
└── docs/
    ├── EXPANDED-ARCHITECTURE-PLAN.md
    ├── ENTERPRISE-CREDENTIAL-SETUP-GUIDE.md
    └── ADVANCED-SYSTEM-DOCUMENTATION.md
```
https://github.com/AnsaAmeen/Real-Estate-Enterprise-Lead-to-Referral-Automation-System/blob/main/Real-estate-Enterprise-Lead-to-Referral-Automation-System.png
https://github.com/AnsaAmeen/Real-Estate-Enterprise-Lead-to-Referral-Automation-System/blob/main/Real-Estate-CRM.png
---

## Tools and Platforms Used

| Tool | Purpose |
|---|---|
| n8n | Core workflow orchestration |
| OpenAI | Lead scoring, sentiment analysis, weekly report generation |
| Airtable | Lead database, conversation history, property inventory, agent roster |
| Twilio | SMS delivery (production) |
| Slack | Internal team alerts, agent notifications |
| Cal.com | Appointment booking |

---

## Setup

Full setup instructions, including Airtable table structures, credential configuration, and environment variables, are documented in `docs/ENTERPRISE-CREDENTIAL-SETUP-GUIDE.md`.

---

## Author

Built by **Ansa Ameen**, Ansa Automation Studio
