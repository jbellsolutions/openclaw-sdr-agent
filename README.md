# OpenClaw AI SDR Team

> 4-5 agent AI SDR system that books 25-50 meetings/month at $4-8 per meeting. Self-improving via Karpathy autoresearch loop. Event-driven. Priority-scored.

**Two versions** — pick the one that fits your stack:

| | v1 Core | v2 Full |
|---|---------|---------|
| **Agents** | 4 (no SMS) | 5 (with SMS) |
| **Email** | Smartlead MCP | Smartlead MCP |
| **Prospecting** | Airtop AI (LinkedIn, FB, IG) | Airtop AI (LinkedIn, FB, IG) |
| **Phone** | Retell AI | Retell AI |
| **SMS** | -- | GoHighLevel |
| **CRM** | JSON files | GoHighLevel + JSON files |
| **Monthly cost** | ~$185 | ~$285 |
| **Best for** | Lean start, email + phone focus | Full multi-channel with CRM |

**Optimized through 10 Karpathy autoresearch loops** — score improved from 19/50 to 47/50. See the [full case study](./docs/Autoresearch-Case-Study.md).

---

## Architecture

### v1 Core (4 Agents — No SMS)

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
```

### v2 Full (5 Agents — SMS + CRM)

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
```

## Agents

### v1 Core

| Agent | Model | Channel | Trigger |
|-------|-------|---------|---------|
| **Prospector** | Haiku | Lead sourcing via Airtop AI | Cron 6 AM ET |
| **Email SDR** | Sonnet | Email via Smartlead MCP | Cron 8 AM + 10 AM ET |
| **Phone SDR** | Retell AI | Calls via Retell AI | Cron 10 AM-12 PM + **speed-to-lead events** |
| **Sequence Manager** | Sonnet | Orchestration | Every 2 hours + 5 PM report |

### v2 Full (adds)

| Agent | Model | Channel | Trigger |
|-------|-------|---------|---------|
| **Text SDR** | Sonnet | SMS via GoHighLevel | Cron 12 PM ET |
| *Sequence Manager* | *Sonnet* | *+ GHL CRM sync* | *Same schedule* |

## Key Innovations

1. **Priority scoring (A/B/C)** — A-leads get phone+email (+SMS in v2). B-leads get email (+SMS in v2). C-leads get email only. Cuts cost per meeting by 40%.

2. **Speed-to-lead** — When an A-lead opens your email, Retell AI calls within 5 minutes. 10x higher connect rate.

3. **Re-engagement triggers** — Cold prospect opens old email after 14 days? Auto-promotes to A-lead with immediate call. Free conversions.

4. **Daily A/B testing** — Each agent tests 2 variants per batch. Results compound into weekly Karpathy self-improvement loop.

5. **Human handoff** — Positive reply detected → stops all automation → sends full brief to owner via Slack/Telegram with talking points + calendar link.

6. **Multi-platform prospecting** — Airtop AI scrapes LinkedIn, Facebook, and Instagram for leads. Find influencers, scrape their post engagement, extract qualified prospects.

## Tool Stack

| Function | Tool | MCP Server | Replaces |
|----------|------|------------|----------|
| **Email outreach** | [Smartlead](https://smartlead.ai) | [smartlead-mcp-server](https://github.com/LeadMagic/smartlead-mcp-server) (116+ tools) | Instantly |
| **Social prospecting** | [Airtop AI](https://airtop.ai) | [airtop-mcp](https://github.com/alecf/airtop-mcp) | Apollo/Apify browser scraping |
| **AI phone calls** | [Retell AI](https://retellai.com) | [retell-mcp-server](https://github.com/sunnysingh100/retell-mcp-server) (60+ tools) | Twilio/deepclaw |
| **SMS + CRM** (v2) | [GoHighLevel](https://gohighlevel.com) | [GoHighLevel-MCP](https://github.com/mastanley13/GoHighLevel-MCP) (269+ tools) | Twilio SMS + JSON CRM |
| **Lead enrichment** | [Apollo](https://apollo.io) | API | Same |
| **Email verification** | [Apify](https://apify.com) | Actor | Same |

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

## Cost Breakdown

### v1 Core (~$185/month)

| Item | Monthly |
|------|---------|
| Orgo VM (SDR agents) | $37 |
| Smartlead (email campaigns) | ~$39-94 |
| Retell AI (voice calls, ~500 min) | ~$35-65 |
| Apollo/Apify (lead data + verification) | ~$50 |
| Airtop AI (browser sessions) | ~$0-20 |
| **Total** | **~$161-266** |

### v2 Full (~$285/month)

| Item | Monthly |
|------|---------|
| Orgo VM (SDR agents) | $37 |
| Smartlead (email campaigns) | ~$39-94 |
| Retell AI (voice calls, ~500 min) | ~$35-65 |
| GoHighLevel (SMS + CRM) | ~$97 |
| Apollo/Apify (lead data + verification) | ~$50 |
| Airtop AI (browser sessions) | ~$0-20 |
| **Total** | **~$258-363** |

## Quick Start

### Prerequisites
- [OpenClaw](https://github.com/open-claw/open-claw) installed
- [Orgo](https://orgo.ai) VM (8GB RAM recommended)
- Smartlead account + API key
- Retell AI account + API key
- Airtop AI account + API key
- Apollo or Apify account (lead sourcing)
- *(v2 only)* GoHighLevel account + Private Integration API key

### Deploy v1 Core

```bash
# 1. Clone this repo
git clone https://github.com/jbellsolutions/openclaw-sdr-agent.git
cd openclaw-sdr-agent

# 2. Copy v1 agent configs to your OpenClaw workspace
cp -r v1-core/agents/ ~/openclaw-workspace/agents/

# 3. Create workspace directories
mkdir -p ~/openclaw-workspace/{leads,calls,sequences,crm,improvements,pipeline}

# 4. Configure API keys in each SOUL.md
# agents/prospector/SOUL.md — Airtop AI + Apollo keys
# agents/email-sdr/SOUL.md — Smartlead API key
# agents/phone-sdr/SOUL.md — Retell AI API key
# agents/sequence-manager/SOUL.md — Slack webhook

# 5. Set up MCP servers in Claude config
# Add: smartlead-mcp, airtop-mcp, retell-mcp-server

# 6. Set up cron jobs
crontab v1-core/cron/crontab.txt

# 7. Test with 10 leads
```

### Deploy v2 Full

```bash
# Same as v1, but use v2-full/ directory
cp -r v2-full/agents/ ~/openclaw-workspace/agents/

# Additional: create SMS directory
mkdir -p ~/openclaw-workspace/sms

# Additional: configure GoHighLevel
# agents/text-sdr/SOUL.md — GHL Private Integration API key
# agents/sequence-manager/SOUL.md — GHL API key

# Additional MCP server: GoHighLevel-MCP
# Set up cron
crontab v2-full/cron/crontab.txt
```

### Scaling

| Tier | Prospects/Day | Agents | Orgo VMs | v1 Cost | v2 Cost |
|------|--------------|--------|----------|---------|---------|
| Starter | 200 | 4-5 | 1 | ~$185 | ~$285 |
| Growth | 500 | 6-8 | 2 | ~$350 | ~$450 |
| Scale | 1,000 | 10-12 | 3 | ~$550 | ~$650 |

## How Self-Improvement Works

Every Friday at 6 PM ET:
1. Sequence Manager pulls weekly A/B test results
2. Identifies top/bottom 10% performing messages
3. Analyzes what pain points, openers, tones convert best
4. Updates each agent's SOUL.md with winning patterns
5. Logs changes to `improvements/week-{N}.md`

The system gets measurably better every week without human intervention.

## File Structure

```
openclaw-sdr-agent/
├── README.md
├── v1-core/                    # 4 agents, no SMS
│   ├── agents/
│   │   ├── prospector/SOUL.md        # Airtop AI + Apollo sourcing
│   │   ├── email-sdr/SOUL.md         # Smartlead MCP campaigns
│   │   ├── phone-sdr/SOUL.md         # Retell AI voice calls
│   │   └── sequence-manager/SOUL.md  # Orchestration + JSON CRM
│   └── cron/crontab.txt
├── v2-full/                    # 5 agents, SMS + GHL CRM
│   ├── agents/
│   │   ├── prospector/SOUL.md        # + GHL CRM sync
│   │   ├── email-sdr/SOUL.md         # + GHL activity logging
│   │   ├── phone-sdr/SOUL.md         # + GHL call logging
│   │   ├── text-sdr/SOUL.md          # GoHighLevel SMS
│   │   └── sequence-manager/SOUL.md  # + GHL CRM as primary
│   └── cron/crontab.txt
├── templates/                  # Shared across versions
│   ├── icp-scoring.md
│   ├── email-templates.md
│   └── call-scripts.md
├── docs/
│   └── Autoresearch-Case-Study.md
└── agents/                     # Legacy (original v0)
    └── ...
```

## Built With

| Tool | Purpose | MCP Tools |
|------|---------|-----------|
| [Smartlead](https://smartlead.ai) | Cold email campaigns + analytics | 116+ |
| [Airtop AI](https://airtop.ai) | Browser automation for LinkedIn/FB/IG scraping | 5+ |
| [Retell AI](https://retellai.com) | AI voice agent for outbound calls | 60+ |
| [GoHighLevel](https://gohighlevel.com) | SMS + CRM + pipeline (v2 only) | 269+ |
| [Apollo](https://apollo.io) | Lead enrichment + contact data | API |
| [Apify](https://apify.com) | Email verification | Actor |
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
