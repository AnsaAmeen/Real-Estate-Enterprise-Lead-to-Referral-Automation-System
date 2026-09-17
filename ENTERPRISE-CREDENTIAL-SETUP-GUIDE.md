# Complete Credential Setup Guide
## Real Estate Enterprise Lead-to-Referral System (50 Nodes)

This guide covers every credential and configuration needed. Set these up
in this order, since some depend on others being ready first.

---

## STEP 1: Airtable Setup (Most Important, Do This First)

This system uses **6 separate Airtable tables**. All of them live inside
one Airtable Base. Set up the base and all tables before touching n8n.

### Creating the Base

1. Go to [airtable.com](https://airtable.com), log in
2. Click **Create a base** → **Start from scratch**
3. Name it something like "Real Estate Automation System"
4. You'll land inside your new base with one default table, delete it once
   the real tables below are created

### Table 1: Conversation History

Fields to create (click the + next to the last column to add each one):
- `Contact` — Single line text
- `Message` — Long text
- `Timestamp` — Date (include time)

### Table 2: Property Inventory

Fields:
- `Address` — Single line text
- `Area` — Single line text
- `Price` — Currency
- `Bedrooms` — Number
- `Status` — Single select (options: Available, Pending, Sold)

Populate this with your actual current listings, this is the real data the
AI references instead of saying "I'll check."

### Table 3: Agent Roster

Fields:
- `Name` — Single line text
- `Territory` — Single line text (e.g., "Downtown", "North Side")
- `Phone` — Single line text (used for Slack DM targeting)
- `Specialty` — Single select (Residential, Commercial, Rentals)

Add one row per real agent on your team.

### Table 4: Bookings

Fields:
- `Contact` — Single line text
- `Viewing Date` — Date (include time)
- `Status` — Single select (options: Scheduled, Completed, No-show)
- `Assigned Agent` — Single line text

### Table 5: All Leads Master Log

Fields:
- `Contact` — Single line text
- `Source` — Single line text
- `Score` — Single select (HOT, WARM, COLD)
- `Summary` — Long text
- `Status` — Single select (Active, Long-term Nurture, Closed)
- `Date` — Date

### Table 6: Cold Leads

Fields:
- `Contact` — Single line text
- `Summary` — Long text

---

### Getting Your Airtable Credentials for n8n

1. Go to your Airtable account, click your profile icon (top right) → **Developer Hub**
2. Click **Personal access tokens** → **Create new token**
3. Name it "n8n Real Estate System"
4. Under **Scopes**, add:
   - `data.records:read`
   - `data.records:write`
5. Under **Access**, select the specific base you just created
6. Click **Create token**, copy it immediately, it will not be shown again

### Getting Your Base ID

1. Open your base in Airtable
2. Click **Help** (top right) → **API documentation**
3. Your Base ID is shown at the top, it starts with `app...`
4. Copy this, you'll paste it into n8n's environment variables (see Step 6 below)

### Connecting Airtable in n8n

1. Click on any Airtable node in the imported workflow (there are 12 of them)
2. Click the Credential dropdown → **Create New Credential**
3. Paste your Personal Access Token
4. Click **Save**, this credential now works for all 12 Airtable nodes automatically

---

## STEP 2: Slack Setup (Detailed)

This system posts to **4 different Slack channels**: `#urgent-leads`,
`#offer-stage`, `#weekly-reports`, `#system-alerts`, plus direct messages to
individual agents.

### Creating the Slack App

1. Go to [api.slack.com/apps](https://api.slack.com/apps)
2. Click **Create New App** → **From scratch**
3. Name it "Real Estate Automation Bot", select your workspace
4. In the left sidebar, click **OAuth & Permissions**
5. Scroll to **Scopes** → **Bot Token Scopes**, add these scopes:
   - `chat:write` (send messages)
   - `chat:write.public` (post to channels without being invited)
   - `im:write` (send direct messages to agents)
   - `users:read` (look up users for direct messaging)
6. Scroll up, click **Install to Workspace**, authorize it
7. Copy the **Bot User OAuth Token** (starts with `xoxb-`)

### Creating the Required Channels

In Slack itself, create these four channels if they don't exist:
- `#urgent-leads`
- `#offer-stage`
- `#weekly-reports`
- `#system-alerts`

### Connecting Slack in n8n

1. Click any Slack node in the workflow (there are 5)
2. Create New Credential, paste the Bot Token
3. Save, this works for all 5 Slack nodes

### About the "Notify Matched Agent Directly" Node Specifically

This node is different from the others, it's meant to message a specific
agent directly, not a shared channel. The current JSON uses the agent's
phone number as a placeholder for the channel field, which will not work
correctly for a direct message.

To fix this properly:
1. In Slack, get each agent's Slack **Member ID** (click their profile → More → Copy Member ID)
2. In your Airtable Agent Roster table, add a new field called `Slack Member ID` and fill it in for each agent
3. In n8n, edit the **Notify Matched Agent Directly** node, change the Channel field to reference this new field instead: `={{$json["assignedAgentSlackId"]}}`
4. Update the **Match Lead to Agent by Territory** code node to also pull and pass along this Slack Member ID field from the roster

This is a small manual adjustment needed because direct messaging requires
a Slack user ID, not a phone number, they're different identifiers.

---

## STEP 3: Cal.com Setup (Detailed)

This handles the actual calendar booking for Hot leads.

### Getting Your Cal.com API Key

1. Go to [cal.com](https://cal.com), sign up or log in
2. Set up at least one **Event Type** (e.g., "Property Viewing - 30 min"),
   this defines the actual bookable slot type
3. Go to **Settings** → **Developer** → **API Keys**
4. Click **Add API Key**, name it "n8n Integration", copy the generated key

### Getting Your Event Type ID

1. Go to your Event Types list in Cal.com
2. Click into the specific event type you want leads to book (e.g., "Property Viewing")
3. The Event Type ID is visible in the URL of that event type's edit page,
   it's the number after `/event-types/`

### Connecting Cal.com in n8n

The **Book Viewing (Cal.com)** node uses a generic HTTP Header authentication,
since Cal.com's n8n integration works through direct API calls rather than
a dedicated credential type.

1. Click the **Book Viewing (Cal.com)** node
2. Click the Credential dropdown → **Create New Credential**
3. For **Name**, enter: `Authorization`
4. For **Value**, enter: `Bearer YOUR_API_KEY_HERE` (replace with your real key)
5. Save

### Setting the Event Type ID

This workflow references `{{$env.CALCOM_EVENT_TYPE_ID}}`, an environment
variable, not a credential. See Step 6 below for how to set this.

---

## STEP 4: Twilio Setup

(Same as the basic version, repeated here for completeness)

1. Go to [twilio.com](https://www.twilio.com), sign up or log in
2. From your Console Dashboard, copy your **Account SID** and **Auth Token**
3. Buy a phone number with Voice and SMS capability if you haven't already
4. In n8n, click any Twilio node (there are 8), create new credential,
   paste Account SID and Auth Token
5. This works for all 8 Twilio nodes automatically

### Configuring Twilio Webhooks

You have 4 separate webhook entry points in this system that need to be
configured in Twilio:
- Missed call detection → points to the **Intake: Missed Call** node's webhook URL
- Incoming SMS replies → points to the **Merge/Intake** flow's webhook URL
- Incoming feedback replies → points to the **Incoming Feedback Reply Webhook** node's URL

Activate the workflow in n8n first to generate real production URLs, then
paste each one into the corresponding Twilio webhook configuration under
Phone Numbers → your number → Voice/Messaging configuration.

---

## STEP 5: OpenAI Setup

1. Go to [platform.openai.com](https://platform.openai.com), create an API key under **API Keys**
2. Ensure your account has a funded balance (small prepaid amount)
3. In n8n, click any OpenAI node (there are 4), create new credential, paste the key
4. This works for all 4 OpenAI nodes automatically

---

## STEP 6: Setting Environment Variables

This workflow references several environment variables directly in the
JSON (things like `{{$env.AIRTABLE_BASE_ID}}`). These are not credentials,
they're separate values you set at the n8n instance level.

If you're using **n8n Cloud**:
1. Go to your instance Settings → Environment Variables
2. Add each of these:
   - `AIRTABLE_BASE_ID` = your Base ID from Step 1
   - `TWILIO_PHONE_NUMBER` = your Twilio number, in format `+1234567890`
   - `CALCOM_EVENT_TYPE_ID` = your Event Type ID from Step 3

If you're **self-hosting n8n**:
1. Add these same three variables to your `.env` file or Docker environment configuration
2. Restart your n8n instance for the variables to load

---

## STEP 7: Full System Test Sequence

Test each major path separately, don't try to test everything at once.

1. **Missed call path:** call your Twilio number, let it go unanswered, confirm the SMS arrives
2. **Reply and scoring path:** reply with a message mentioning a real property from your inventory, confirm the AI response references it correctly
3. **Hot lead path:** send a message with a specific property, budget, and timeline, confirm a Cal.com booking gets created and the correct agent gets notified based on territory
4. **Urgent override path:** send a message with frustrated language, confirm it skips scoring and goes straight to `#urgent-leads`
5. **Reminder path:** manually add a test booking in Airtable dated for tomorrow, wait for the 6pm daily trigger, confirm the reminder SMS sends
6. **No-show path:** manually add a booking dated yesterday with Status still "Scheduled," confirm it gets flagged and a rebooking SMS sends
7. **Feedback path:** manually mark a booking "Completed" for yesterday, confirm the feedback request sends, then reply to test both INTERESTED and NOT_INTERESTED branches
8. **Closing path:** manually change a lead's Status to "Closed" in the Master Log table, confirm the thank you message sends (the referral message will take 3 weeks to fire, that part can't be tested quickly)
9. **Weekly report:** wait for Monday 8am, or temporarily change the schedule trigger to fire sooner for testing purposes, confirm the report posts to `#weekly-reports`

---

## Common Setup Mistakes Specific to This Larger System

- **Airtable field name mismatches:** every field name in this guide must match exactly, including spaces and capitalization, or nodes fail silently
- **Slack Member ID vs phone number confusion:** direct agent messaging will not work until you complete the fix described in Step 2
- **Environment variables not set:** if `{{$env.AIRTABLE_BASE_ID}}` shows as literally that text instead of your real Base ID anywhere in the system, the environment variable wasn't saved correctly
- **Testing the 3-week referral delay:** there's no realistic way to fast-test a 3-week wait node, trust the logic once the "Send Thank You Message" step is confirmed working, or temporarily lower the wait time to a few minutes for testing, then change it back
