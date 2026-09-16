# Real-Estate-Enterprise-Lead-to-Referral-Automation-System
An n8n automation system that manages the complete real estate lead lifecycle, from first contact through closing and referral, with AI-based lead scoring, territory-based agent routing, automated nurture sequences, and appointment/no-show handling.
What This System Does
Captures leads from three channels simultaneously: missed calls, website form submissions, and WhatsApp messages
Checks incoming messages for urgency or frustration and escalates those directly to a human, bypassing normal scoring
Uses an AI agent to score every lead as Hot, Warm, or Cold, referencing real property inventory and prior conversation history rather than guessing
Routes Hot leads to the correct agent based on territory
Sends automated appointment reminders and detects no-shows, triggering an automatic rebooking sequence
Collects post-viewing feedback and escalates interested leads to a human for the offer stage
Automatically sends a thank you message and, three weeks later, a referral request once a deal is marked closed
Generates and sends a weekly summary report of all lead activity
Includes dedicated error handling that alerts the team the moment any part of the system fails
Current Status: Testing Phase

This system is fully built and functional in n8n, with 50 connected nodes covering the entire lifecycle described above. It is currently in a testing phase, with one specific limitation worth noting honestly.

Twilio SMS is not usable from the developer's current location (Pakistan has restrictions affecting standard Twilio SMS delivery). To continue testing and validating the full workflow logic during development, several outbound messaging steps (appointment reminders, nurture messages, feedback requests, and referral messages) are temporarily routed to Slack instead of actual SMS, so the logic and timing of every step could be fully tested without a working SMS channel.

In a production deployment, these steps are designed to send real SMS or WhatsApp messages directly to the lead, not internal Slack notifications. Switching them back is a configuration change, not a redesign, since the node types (Twilio) already exist in the workflow and are simply disabled during this testing phase. For clients outside regions with this restriction, or once a WhatsApp Business API or alternative SMS provider is connected, this reverts to fully automated real customer messaging.

This kind of temporary substitution during development, and reverting it before going live, is standard practice, this note exists for transparency, not because the system doesn't work.
