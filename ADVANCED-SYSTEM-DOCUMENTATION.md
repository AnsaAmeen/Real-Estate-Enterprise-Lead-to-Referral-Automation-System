# Advanced System: Real Estate Lead-to-Close Automation
## Full Lifecycle, Multi-Channel, AI-Scored, Auto-Routed

---

## What Makes This "Advanced" vs the Basic Version

The basic version only handled one entry point (missed calls) and one action
(SMS + log). This system handles the entire lead lifecycle:

1. **Multi-channel intake** — captures leads from missed calls, website
   forms, and WhatsApp messages simultaneously, normalizing all three into
   one consistent lead format
2. **AI-based lead scoring** — an AI agent doesn't just reply, it actually
   scores the lead (Hot, Warm, Cold) based on intent, budget signals, and
   urgency, using structured function calling so the output is reliable data,
   not just a text guess
3. **Automatic routing by score** — Hot leads get an instant calendar
   booking link and immediate agent alert. Warm leads enter a multi-day
   nurture sequence. Cold leads get logged for long-term follow-up, no
   immediate action wasted on them
4. **Real calendar booking** — Hot leads can book a viewing directly through
   the AI conversation, no back and forth
5. **Automated nurture sequence** — Warm leads receive a spaced sequence of
   follow-up messages over several days, not a single message and silence
6. **Weekly reporting** — every Monday, an AI generated summary of the
   week's leads, scores, and outcomes gets sent to the agency owner
   automatically, no one has to manually pull a report
7. **Error handling** — if any part of the system fails, the team gets
   alerted immediately instead of leads silently disappearing

---

## Total Node Count: 25

## Full Lifecycle Map

```
THREE INTAKE SOURCES (run independently, in parallel)
  Missed Call Webhook ─┐
  Website Form Webhook ─┼─→ Normalize each into same format ─→ Merge
  WhatsApp Webhook ─────┘

AFTER MERGE (every lead goes through this)
  AI Lead Scoring Agent (outputs: score, intent, budget, timeline, summary)
        ↓
  Switch: Route by Score
        ↓
  ┌─────────────┬──────────────┬─────────────┐
  HOT             WARM            COLD
  ↓               ↓               ↓
  Book Calendar   Wait 1 Day      Log to Airtable
  Send Confirm    Nurture Msg 1        (long term list)
  Alert Agent     Wait 3 Days
  (now)           Nurture Msg 2

ALL LEADS (regardless of score)
  → Logged to master Airtable table

SEPARATE WEEKLY TRIGGER (runs every Monday 8am)
  → Pull this week's leads from Airtable
  → AI generates a plain language summary
  → Sent to agency owner via Slack/Email

ERROR HANDLING (runs on any node failure, anywhere in the system)
  → Immediate Slack alert to the technical owner
```

---

## AI Lead Scoring — System Prompt

This is a different, more advanced prompt than the basic version. It uses
function calling so the AI returns structured data (a real score n8n can
route on), not just a conversational reply.

```
You are a lead scoring and qualification agent for a real estate agency.
You will receive a message from a potential lead, along with the channel
they contacted us through (missed call SMS reply, website form, or
WhatsApp).

Analyze the message and return a structured assessment using the
score_lead function. Do not respond conversationally, only call the function.

Scoring guide:
- HOT: mentions a specific property, a specific budget or price range, a
  specific timeline (this week, this month), or explicitly asks to view or
  book something
- WARM: general interest in buying, renting, or selling, but no specific
  property, budget, or timeline mentioned yet
- COLD: vague browsing, unclear intent, or a message that doesn't indicate
  genuine near term interest

Always extract whatever real information is present. Never invent a budget,
timeline, or property interest that wasn't actually stated.
```

### Function Definition (Structured Output)

```json
{
  "name": "score_lead",
  "description": "Score and categorize a real estate lead based on their message",
  "parameters": {
    "type": "object",
    "properties": {
      "score": {
        "type": "string",
        "enum": ["HOT", "WARM", "COLD"]
      },
      "intent": {
        "type": "string",
        "enum": ["buying", "renting", "selling", "unclear"]
      },
      "budget_mentioned": {
        "type": "string",
        "description": "The exact budget or price range if mentioned, otherwise 'not mentioned'"
      },
      "timeline_mentioned": {
        "type": "string",
        "description": "The exact timeline if mentioned, otherwise 'not mentioned'"
      },
      "summary": {
        "type": "string",
        "description": "One sentence summary of what the lead actually said"
      }
    },
    "required": ["score", "intent", "budget_mentioned", "timeline_mentioned", "summary"]
  }
}
```

---

## Nurture Sequence Messages (Warm Leads)

**Message 1 (sent after 1 day of silence):**
```
Hi again! Just checking in, still exploring options in the area you
mentioned? Happy to send over a few that might fit if you let me know
what you're looking for.
```

**Message 2 (sent after 3 more days of silence):**
```
No pressure at all, just wanted to stay on your radar. If your timeline
shifts or you'd like to see what's currently available, just reply here
anytime.
```

---

## Weekly Report — AI Summary Prompt

```
You are generating a weekly lead summary for a real estate agency owner.
You will receive a list of this week's leads with their scores, intents,
and summaries. Write a short, plain language report, no more than 6
sentences, covering:
1. Total number of leads this week
2. Breakdown by score (how many hot, warm, cold)
3. Any notable pattern worth mentioning (e.g., a specific property getting
   repeated interest)
4. One sentence on what needs attention, if anything

Do not invent numbers or patterns that aren't actually present in the data provided.
```

---

## Why This Structure Matters When Pitching a Client

This is the difference between showing a client "I can send an automatic
text" versus showing them "I built a system that scores every lead, books
your hottest prospects automatically, nurtures your maybes without you
lifting a finger, and emails you a summary every Monday morning." The second
one is what actually justifies a real project fee.
