# OpenClaw AI SDR Team

> 4-5 agent AI SDR system that books 25-50 meetings/month at $4-8 per meeting. Self-improving via Karpathy autoresearch loop. Event-driven. Priority-scored. Composio-powered.

---

## Choose Your Version

This repo ships **two versions**. Pick the one that matches your stack:

### v1 Core — Lean Start (Email + Phone)

**Best for**: Getting started fast, proving the system works, lower monthly cost.

- **4 agents** — Prospector, Email SDR, Phone SDR, Sequence Manager
- **No SMS** — email and phone only
- **JSON CRM** — lightweight, agent-readable files
- **~$185/month**

**Use v1 if**: You want to validate the pipeline before adding more channels, or you don't have GoHighLevel yet.

```bash
# Deploy v1
cp -r v1-core/agents/ ~/openclaw-workspace/agents/
crontab v1-core/cron/crontab.txt
```

### v2 Full — Multi-Channel + CRM (Email + Phone + SMS)

**Best for**: Full multi-channel outreach with a real CRM dashboard.

- **5 agents** — Everything in v1, plus Text SDR
- **SMS via GoHighLevel** — Day 3 + Day 7 follow-ups to A+B leads
- **GoHighLevel CRM** — visual pipeline, unified inbox, automation workflows
- **~$285/month**

**Use v2 if**: You have GoHighLevel, want SMS as a channel, and want a visual CRM dashboard instead of JSON files.

```bash
# Deploy v2
cp -r v2-full/agents/ ~/openclaw-workspace/agents/
crontab v2-full/cron/crontab.txt
```

### Side-by-Side Comparison

| | v1 Core | v2 Full |
|---|---------|---------|
| **Agents** | 4 (no SMS) | 5 (with SMS) |
| **Email** | Smartlead MCP | Smartlead MCP |
| **Prospecting** | Airtop AI (LinkedIn, FB, IG) | Airtop AI (LinkedIn, FB, IG) |
| **Phone** | Retell AI | Retell AI |
| **SMS** | -- | GoHighLevel |
| **CRM** | JSON files | GoHighLevel + JSON files |
| **Integration layer** | Composio (982+ tools) | Composio (982+ tools) |
| **Monthly cost** | ~$185 | ~$285 |
| **Meetings/month** | 20-40 | 25-50 |
| **Setup time** | 1-2 days | 2-3 days |

**Both versions include**: Composio integration layer, Karpathy self-improvement loop, A/B testing, speed-to-lead triggers, human handoff protocol.

---

## Architecture

### v1 Core (4 Agents)

```
Prospector → leads/today.json (A/B/C scored + verified)
    ↓         (Airtop AI: LinkedIn/FB/IG + Apollo enrichment)
Email SDR → sequences/email/{id}.json → Smartlead MCP (A/B test variants)
    ↓ (opens/clicks → trigger Phone SDR immediately for A-leads)
Phone SDR → calls/YYYY-MM-DD/{id}.json → Retell AI (A-leads only)
    ↓
Sequence Manager → crm/ (pipeline) → Slack #sdr (reports) → DM owner (hot leads)
    ↓ (weekly)
Self-Improvement Loop → improvements/week-{N}.md → updates all SOUL.md files

    ┌─────────────────────────────────────────────┐
    │  COMPOSIO (982+ tools via MCP)              │
    │  HubSpot, Calendly, Google Calendar, Gmail, │
    │  WhatsApp, Slack, Notion, Salesforce, ...    │
    │  Any agent can call any Composio tool        │
    └─────────────────────────────────────────────┘
```

### v2 Full (5 Agents)

```
Prospector → leads/today.json → GoHighLevel CRM
    ↓         (Airtop AI: LinkedIn/FB/IG + Apollo enrichment)
Email SDR → sequences/email/{id}.json → Smartlead MCP → GHL CRM
    ↓ (opens/clicks → trigger Phone SDR immediately for A-leads)
Phone SDR → calls/YYYY-MM-DD/{id}.json → Retell AI → GHL CRM
    ↓ (non-responders)
Text SDR → sms/YYYY-MM-DD/{id}.json → GoHighLevel SMS (A+B leads)
    ↓
Sequence Manager → GHL CRM (pipeline) → Slack #sdr → DM owner (hot leads)
    ↓ (weekly)
Self-Improvement Loop → improvements/week-{N}.md → updates all SOUL.md files

    ┌─────────────────────────────────────────────┐
    │  COMPOSIO (982+ tools via MCP)              │
    │  HubSpot, Calendly, Google Calendar, Gmail, │
    │  WhatsApp, Slack, Notion, Salesforce, ...    │
    │  Any agent can call any Composio tool        │
    └─────────────────────────────────────────────┘
```

---

## Composio — 982+ Tool Integration Layer

[Composio](https://composio.dev) gives every agent access to 982+ external tools via MCP. Instead of building custom integrations for each service, Composio handles authentication, tool discovery, and execution.

### Why Composio Matters for SDR

| What You Get | Tools Available | How Agents Use It |
|-------------|----------------|-------------------|
| **CRM sync** | HubSpot, Salesforce, Pipedrive | Push leads, update deals, log activity |
| **Calendar booking** | Google Calendar, Calendly | Book meetings directly when prospect says yes |
| **Email** | Gmail, Outlook | Read replies, send follow-ups outside Smartlead |
| **Messaging** | WhatsApp, Slack, Microsoft Teams | Multi-channel prospect communication |
| **Enrichment** | Fullenrich, Veriphone, Brandfetch | Verify phones, enrich contacts, pull company data |
| **Call intelligence** | Gong | Analyze call recordings, extract coaching insights |
| **Project tracking** | Linear, Asana, Airtable | Track SDR tasks, pipeline operations |

### Setup

```bash
# 1. Install Composio CLI
curl -fsSL https://composio.dev/install | bash

# 2. Login
composio login

# 3. Add tools you need (example: Google Calendar + HubSpot)
composio add google-calendar
composio add hubspot

# 4. Get your MCP server URL
composio mcp setup

# 5. Add to your OpenClaw/Claude config as an MCP server
# The URL format: https://backend.composio.dev/v3/mcp/[SERVER_ID]?user_id=[USER_ID]
```

### What This Unlocks

With Composio, your agents can:
- **Sequence Manager** → Book meetings directly into Google Calendar or Calendly when a prospect says "yes"
- **Prospector** → Push leads to HubSpot/Salesforce CRM alongside GHL
- **Phone SDR** → Check prospect's calendar availability before suggesting times
- **Email SDR** → Pull prospect's recent LinkedIn activity via Composio browser tools
- **Any agent** → Send WhatsApp messages, update Notion databases, trigger Zapier workflows

### Clawdi AI Reference

[Clawdi AI](https://www.clawdi.ai/) is a managed OpenClaw deployment platform that demonstrates how OpenClaw + Composio work together in production. It provides:
- AI chief of staff that lives in Telegram, Discord, WhatsApp, Slack, and email
- 500+ integrated tools (powered by Composio)
- Confidential VMs with Intel TDX encryption
- One-click deployment of OpenClaw agents

**For your OCA client model**: Clawdi shows what a productized OpenClaw + Composio deployment looks like. You can either use Clawdi for client deployments or build your own version on Orgo VMs.

---

## Agents

### v1 Core — 4 Agents

| Agent | Model | Channel | Tools | Trigger |
|-------|-------|---------|-------|---------|
| **Prospector** | Haiku | Lead sourcing | Airtop AI, Apollo, Composio | Cron 6 AM ET |
| **Email SDR** | Sonnet | Email | Smartlead MCP, Composio | Cron 8 AM + 10 AM ET |
| **Phone SDR** | Retell AI | Calls | Retell AI MCP, Composio | Cron 10 AM-12 PM + speed-to-lead |
| **Sequence Manager** | Sonnet | Orchestration | Smartlead, Retell, Composio | Every 2 hrs + 5 PM report |

### v2 Full — 5 Agents (adds)

| Agent | Model | Channel | Tools | Trigger |
|-------|-------|---------|-------|---------|
| **Text SDR** | Sonnet | SMS | GoHighLevel MCP, Composio | Cron 12 PM ET |
| *Sequence Manager* | *Sonnet* | *+ GHL CRM* | *+ GoHighLevel MCP* | *Same* |

---

## Key Innovations

1. **Priority scoring (A/B/C)** — A-leads get phone+email (+SMS in v2). B-leads get email (+SMS in v2). C-leads get email only. Cuts cost per meeting by 40%.

2. **Speed-to-lead** — When an A-lead opens your email, Retell AI calls within 5 minutes. 10x higher connect rate.

3. **Re-engagement triggers** — Cold prospect opens old email after 14 days? Auto-promotes to A-lead with immediate call. Free conversions.

4. **Daily A/B testing** — Each agent tests 2 variants per batch. Results compound into weekly Karpathy self-improvement loop.

5. **Human handoff** — Positive reply detected → stops all automation → sends full brief to owner via Slack/Telegram with talking points + calendar link.

6. **Multi-platform prospecting** — Airtop AI scrapes LinkedIn, Facebook, and Instagram for leads. Find influencers, scrape their post engagement, extract qualified prospects.

7. **982+ tool access via Composio** — Any agent can book calendar meetings, sync to CRM, send WhatsApp messages, or trigger workflows across 982+ integrated apps.

---

## Tool Stack

| Function | Tool | Integration | Notes |
|----------|------|------------|-------|
| **Email outreach** | [Smartlead](https://smartlead.ai) | [MCP server](https://github.com/LeadMagic/smartlead-mcp-server) (116+ tools) | Campaigns, warmup, analytics |
| **Social prospecting** | [Airtop AI](https://airtop.ai) | [MCP server](https://github.com/alecf/airtop-mcp) | LinkedIn, FB, IG browser automation |
| **AI phone calls** | [Retell AI](https://retellai.com) | [MCP server](https://github.com/sunnysingh100/retell-mcp-server) (60+ tools) | Voice agent, transcription, objections |
| **SMS + CRM** (v2) | [GoHighLevel](https://gohighlevel.com) | [MCP server](https://github.com/mastanley13/GoHighLevel-MCP) (269+ tools) | SMS, CRM, pipeline, automation |
| **Integration layer** | [Composio](https://composio.dev) | [MCP server](https://docs.composio.dev/docs/mcp/overview) (982+ tools) | CRM, calendar, messaging, enrichment |
| **Lead enrichment** | [Apollo](https://apollo.io) | API | Contact data, company info |
| **Email verification** | [Apify](https://apify.com) | Actor | $0.001/email verification |

---

## Conversion Math

### v1 Core (Email + Phone)

| Channel | Prospects/Day | Conversion | Warm Leads/Month |
|---------|--------------|------------|-----------------|
| A-leads (email+phone) | 40 | 12% | 106 |
| B-leads (email only) | 100 | 3% | 66 |
| C-leads (email only) | 60 | 2% | 26 |
| **Combined** | **200** | | **~198 warm → 20-40 meetings** |

### v2 Full (Email + Phone + SMS)

| Channel | Prospects/Day | Conversion | Warm Leads/Month |
|---------|--------------|------------|-----------------|
| A-leads (email+phone+SMS) | 40 | 15% | 132 |
| B-leads (email+SMS) | 100 | 5% | 110 |
| C-leads (email only) | 60 | 2% | 26 |
| **Combined** | **200** | | **~268 warm → 25-50 meetings** |

---

## Cost Breakdown

### v1 Core

| Item | Monthly |
|------|---------|
| Orgo VM (SDR agents) | $37 |
| Smartlead (email campaigns) | ~$39-94 |
| Retell AI (voice calls, ~500 min) | ~$35-65 |
| Apollo/Apify (lead data + verification) | ~$50 |
| Airtop AI (browser sessions) | ~$0-20 |
| Composio (integration layer) | Free tier / ~$29+ |
| **Total** | **~$190-295** |

### v2 Full

| Item | Monthly |
|------|---------|
| Orgo VM (SDR agents) | $37 |
| Smartlead (email campaigns) | ~$39-94 |
| Retell AI (voice calls, ~500 min) | ~$35-65 |
| GoHighLevel (SMS + CRM) | ~$97 |
| Apollo/Apify (lead data + verification) | ~$50 |
| Airtop AI (browser sessions) | ~$0-20 |
| Composio (integration layer) | Free tier / ~$29+ |
| **Total** | **~$287-392** |

---

## Setup Guide

### Prerequisites (Both Versions)

1. [OpenClaw](https://github.com/open-claw/open-claw) installed
2. [Orgo](https://orgo.ai) VM (8GB RAM recommended)
3. API keys for: Smartlead, Retell AI, Airtop AI, Apollo/Apify
4. Composio account (free tier available)
5. *(v2 only)* GoHighLevel account with Private Integration API key

### Step-by-Step: Deploy v1 Core

```bash
# 1. Clone this repo
git clone https://github.com/jbellsolutions/openclaw-sdr-agent.git
cd openclaw-sdr-agent

# 2. Copy v1 agent configs
cp -r v1-core/agents/ ~/openclaw-workspace/agents/

# 3. Create workspace directories
mkdir -p ~/openclaw-workspace/{leads,calls,sequences,crm,improvements,pipeline}

# 4. Set up Composio (integration layer for all agents)
curl -fsSL https://composio.dev/install | bash
composio login
composio add google-calendar    # for meeting booking
composio add hubspot            # optional: CRM sync
composio mcp setup              # get your MCP server URL

# 5. Configure MCP servers in your OpenClaw/Claude config:
#    - Smartlead MCP (npm install -g smartlead-mcp-by-leadmagic)
#    - Airtop MCP (clone https://github.com/alecf/airtop-mcp)
#    - Retell AI MCP (clone https://github.com/sunnysingh100/retell-mcp-server)
#    - Composio MCP (URL from step 4)

# 6. Configure API keys in each agent SOUL.md:
#    agents/prospector/SOUL.md    → Airtop AI + Apollo keys
#    agents/email-sdr/SOUL.md    → Smartlead API key
#    agents/phone-sdr/SOUL.md    → Retell AI API key
#    agents/sequence-manager/SOUL.md → Slack webhook URL

# 7. Set up cron jobs
crontab v1-core/cron/crontab.txt

# 8. Test with 10 leads
#    Drop 10 test leads into leads/today.json and watch the pipeline

# 9. Validate end-to-end
#    - Check Smartlead for sent emails
#    - Check Retell AI dashboard for call logs
#    - Check Slack #sdr for daily report
```

### Step-by-Step: Deploy v2 Full

```bash
# Steps 1-3: Same as v1, but use v2-full/
cp -r v2-full/agents/ ~/openclaw-workspace/agents/
mkdir -p ~/openclaw-workspace/{leads,calls,sequences,sms,crm,improvements,pipeline}

# Step 4: Same Composio setup as v1

# Step 5: Same MCP servers as v1, PLUS:
#    - GoHighLevel MCP (clone https://github.com/mastanley13/GoHighLevel-MCP)

# Step 6: Same as v1, PLUS:
#    agents/text-sdr/SOUL.md         → GHL Private Integration API key
#    agents/sequence-manager/SOUL.md  → GHL API key (for CRM sync)

# Step 7: Use v2 cron
crontab v2-full/cron/crontab.txt

# Step 8-9: Same validation as v1, PLUS:
#    - Check GHL dashboard for contacts and pipeline
#    - Check GHL conversations for SMS threads
```

### Upgrading v1 → v2

Already running v1 and want to add SMS + CRM? Simple:

```bash
# 1. Copy v2 agents (overwrites v1 agents with GHL-aware versions)
cp -r v2-full/agents/ ~/openclaw-workspace/agents/

# 2. Create SMS directory
mkdir -p ~/openclaw-workspace/sms

# 3. Set up GoHighLevel MCP server

# 4. Configure GHL API keys in SOUL.md files

# 5. Update cron
crontab v2-full/cron/crontab.txt

# 6. Import existing crm/prospects.json into GHL as contacts
```

Your existing leads, sequences, and improvement history carry over — v2 just adds the SMS channel and GHL CRM on top.

---

## Scaling

| Tier | Prospects/Day | Agents | Orgo VMs | v1 Cost | v2 Cost | Meetings |
|------|--------------|--------|----------|---------|---------|----------|
| Starter | 200 | 4-5 | 1 | ~$190 | ~$290 | 20-50 |
| Growth | 500 | 6-8 | 2 | ~$350 | ~$450 | 50-120 |
| Scale | 1,000 | 10-12 | 3 | ~$550 | ~$650 | 100-250 |

---

## How Self-Improvement Works

Every Friday at 6 PM ET:
1. Sequence Manager pulls weekly A/B test results
2. Identifies top/bottom 10% performing messages
3. Analyzes what pain points, openers, tones convert best
4. Updates each agent's SOUL.md with winning patterns
5. Logs changes to `improvements/week-{N}.md`

The system gets measurably better every week without human intervention.

---

## File Structure

```
openclaw-sdr-agent/
├── README.md
├── v1-core/                          # ← USE THIS for email + phone only
│   ├── agents/
│   │   ├── prospector/SOUL.md              # Airtop AI + Apollo + Composio
│   │   ├── email-sdr/SOUL.md               # Smartlead MCP + Composio
│   │   ├── phone-sdr/SOUL.md               # Retell AI MCP + Composio
│   │   └── sequence-manager/SOUL.md        # Orchestration + JSON CRM + Composio
│   └── cron/crontab.txt
├── v2-full/                          # ← USE THIS for email + phone + SMS + CRM
│   ├── agents/
│   │   ├── prospector/SOUL.md              # + GHL CRM sync
│   │   ├── email-sdr/SOUL.md               # + GHL activity logging
│   │   ├── phone-sdr/SOUL.md               # + GHL call logging
│   │   ├── text-sdr/SOUL.md                # GoHighLevel SMS
│   │   └── sequence-manager/SOUL.md        # + GHL CRM as primary
│   └── cron/crontab.txt
├── templates/                        # Shared across both versions
│   ├── icp-scoring.md                      # ICP qualification rubric
│   ├── email-templates.md                  # Starter email templates
│   └── call-scripts.md                     # Starter call scripts
├── docs/
│   └── Autoresearch-Case-Study.md          # How this system was optimized
└── agents/                           # Legacy (original v0 with Instantly/Twilio)
    └── ...
```

---

## Built With

| Tool | Purpose | MCP Tools |
|------|---------|-----------|
| [Smartlead](https://smartlead.ai) | Cold email campaigns + analytics | 116+ |
| [Airtop AI](https://airtop.ai) | Browser automation for LinkedIn/FB/IG | 5+ |
| [Retell AI](https://retellai.com) | AI voice agent for outbound calls | 60+ |
| [Composio](https://composio.dev) | Integration layer (CRM, calendar, messaging) | 982+ |
| [GoHighLevel](https://gohighlevel.com) | SMS + CRM + pipeline (v2 only) | 269+ |
| [Apollo](https://apollo.io) | Lead enrichment + contact data | API |
| [Apify](https://apify.com) | Email verification | Actor |
| [Clawdi AI](https://www.clawdi.ai/) | Reference: managed OpenClaw + Composio | -- |
| [OpenClaw](https://github.com/open-claw/open-claw) | Agent framework | -- |
| [Orgo](https://orgo.ai) | Cloud desktop for browser agents | -- |

## Optimization History

This architecture was optimized using the [Karpathy autoresearch pattern](./docs/Autoresearch-Case-Study.md):
- **10 runs**, 30 variations evaluated
- **7 kept, 3 discarded**
- Score: **19/50 → 47/50** (+147% improvement)
- Biggest breakthroughs: priority scoring (Run 4), CRM data layer (Run 6), speed-to-lead triggers (Run 9)

## License

MIT
