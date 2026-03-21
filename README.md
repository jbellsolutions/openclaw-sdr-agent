# OpenClaw AI SDR Team

> 5-agent AI SDR system that books 25-50 meetings/month at $3.90-7.80 per meeting. Self-improving via Karpathy autoresearch loop. Event-driven. Priority-scored.

**Optimized through 10 Karpathy autoresearch loops** — score improved from 19/50 to 47/50. See the [full case study](./docs/Autoresearch-Case-Study.md).

---

## Architecture

```
Prospector → leads/today.json (A/B/C scored + verified)
    ↓
Email SDR → sequences/email/{id}.json → Instantly API (A/B test variants)
    ↓ (opens/clicks → trigger Phone SDR immediately for A-leads)
Phone SDR → calls/YYYY-MM-DD/{id}.json (A-leads only + re-engagement)
    ↓ (non-responders)
Text SDR → sms/YYYY-MM-DD/{id}.json → Twilio (A+B leads)
    ↓
Sequence Manager → crm/ (pipeline) → Slack #sdr (reports) → DM owner (hot leads)
    ↓ (weekly)
Self-Improvement Loop → improvements/week-{N}.md → updates all SOUL.md files
```

## 5 Agents

| Agent | Model | Channel | Trigger |
|-------|-------|---------|---------|
| **Prospector** | Haiku | Lead sourcing | Cron 6 AM ET |
| **Email SDR** | Sonnet | Email via Instantly | Cron 8 AM + 10 AM ET |
| **Phone SDR** | OpenClaw Voice | Calls via Twilio | Cron 10 AM-12 PM + **speed-to-lead events** |
| **Text SDR** | Sonnet | SMS via Twilio | Cron 12 PM ET |
| **Sequence Manager** | Sonnet | Orchestration | Every 2 hours + 5 PM report |

## Key Innovations

1. **Priority scoring (A/B/C)** — A-leads get phone+email+SMS. B-leads get email+SMS. C-leads get email only. Cuts cost per meeting by 40%.

2. **Speed-to-lead** — When an A-lead opens your email, Phone SDR calls within 5 minutes. 10x higher connect rate.

3. **Re-engagement triggers** — Cold prospect opens old email after 14 days? Auto-promotes to A-lead with immediate call. Free conversions.

4. **Daily A/B testing** — Each agent tests 2 variants per batch. Results compound into weekly Karpathy self-improvement loop.

5. **Human handoff** — Positive reply detected → stops all automation → sends full brief to owner via Slack/Telegram with talking points + calendar link.

6. **CRM data layer** — `crm/` directory readable by all other agents (Co-Founder, CFO, GTM Expert).

## Conversion Math

| Channel | Prospects/Day | Conversion | Warm Leads/Month |
|---------|--------------|------------|-----------------|
| A-leads (email+phone+SMS) | 40 | 15% | 132 |
| B-leads (email+SMS) | 100 | 5% | 110 |
| C-leads (email only) | 60 | 2% | 26 |
| **Combined** | **200** | | **~268 warm → 25-50 meetings** |

**Cost: ~$224/month | Cost per meeting: $3.90-7.80**

## Cost Breakdown

| Item | Monthly |
|------|---------|
| Orgo VM | $37 |
| Twilio (reduced by tiering) | ~$35 |
| Instantly | ~$97 |
| Apollo/Apify | ~$50 |
| Email verification | ~$5 |
| **Total** | **~$224/month** |

## Quick Start

### Prerequisites
- [OpenClaw](https://github.com/open-claw/open-claw) installed
- [Orgo](https://orgo.ai) VM (8GB RAM recommended)
- Twilio account (phone + SMS)
- Instantly account (email sending)
- Apollo or Apify account (lead sourcing)

### Week 1 Deployment

```bash
# 1. Clone this repo into your OpenClaw workspace
git clone https://github.com/jbellsolutions/openclaw-sdr-agent.git
cd openclaw-sdr-agent

# 2. Copy agent configs to your OpenClaw workspace
cp -r agents/ ~/openclaw-workspace/agents/

# 3. Create workspace directories
mkdir -p ~/openclaw-workspace/{leads,calls,sequences,sms,crm,improvements,pipeline}

# 4. Configure your ICP and API keys in each SOUL.md
# Edit agents/prospector/SOUL.md — set your ICP criteria
# Edit agents/email-sdr/SOUL.md — set your Instantly API key
# Edit agents/phone-sdr/SOUL.md — set your Twilio credentials
# Edit agents/text-sdr/SOUL.md — set your Twilio SMS number
# Edit agents/sequence-manager/SOUL.md — set your Slack webhook

# 5. Set up cron jobs (see cron/crontab.txt)
crontab cron/crontab.txt

# 6. Test with 10 leads
# Drop 10 test leads into leads/today.json and watch the pipeline
```

### Scaling

| Tier | Prospects/Day | Agents | Orgo VMs | Monthly Cost |
|------|--------------|--------|----------|-------------|
| Starter | 200 | 5 | 1 | ~$224 |
| Growth | 500 | 8 | 2 | ~$400 |
| Scale | 1,000 | 12 | 3 | ~$650 |

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
├── agents/
│   ├── prospector/
│   │   └── SOUL.md          # Lead sourcing + A/B/C scoring
│   ├── email-sdr/
│   │   └── SOUL.md          # Email personalization + A/B testing
│   ├── phone-sdr/
│   │   └── SOUL.md          # Voice calls + speed-to-lead + objections
│   ├── text-sdr/
│   │   └── SOUL.md          # SMS outreach + opt-out handling
│   └── sequence-manager/
│       └── SOUL.md          # Orchestration + handoff + reporting
├── cron/
│   └── crontab.txt          # All cron schedules
├── templates/
│   ├── icp-scoring.md       # ICP qualification rubric
│   ├── email-templates.md   # Starter email templates for A/B testing
│   └── call-scripts.md      # Starter call scripts for A/B testing
└── docs/
    └── Autoresearch-Case-Study.md
```

## Built With

| Tool | Purpose |
|------|---------|
| [OpenClaw](https://github.com/open-claw/open-claw) | Agent framework (multi-channel inbox) |
| [awesome-openclaw-agents](https://github.com/mergisi/awesome-openclaw-agents) | SDR template base |
| [clawphone](https://github.com/ranacseruet/clawphone) | Twilio phone/SMS integration |
| [deepclaw](https://github.com/deepgram/deepclaw) | Voice agent for outbound calls |
| [Orgo](https://orgo.ai) | Cloud desktop for browser-based prospecting |

## Optimization History

This architecture was optimized using the [Karpathy autoresearch pattern](./docs/Autoresearch-Case-Study.md):
- **10 runs**, 30 variations evaluated
- **7 kept, 3 discarded**
- Score: **19/50 → 47/50** (+147% improvement)
- Biggest breakthroughs: priority scoring (Run 4), CRM data layer (Run 6), speed-to-lead triggers (Run 9)

## License

MIT
