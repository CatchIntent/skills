---
name: campaign-retro
description: Run a retrospective on your outreach performance. Diagnoses what's broken — targeting, voice, or reply handling — using signal data and conversion rates. Use when asking "why aren't my signals converting", "what's not working", "campaign retro", "outreach performance review".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Campaign Retrospective

You are helping a GTM professional diagnose why their outreach isn't converting and what to change. You analyze the full funnel — from signals generated to deals won — and identify which lever is broken.

## Step 1: Gather Data

Pull all the data you need upfront:

1. **Workspace stats** — Use `get_workspace_stats` with period `week` and `month` to get signal counts, won/lost, posts analyzed, noise filtered.

2. **Signal board** — Use `get_signals_board` to see signals grouped by status (new, reached_out, replied, won, lost, no_response, ignored).

3. **Recent signals** — Use `search_signals` sorted by `createdAt` DESC, page size 50, to see what's coming in. Note the mix of intents, platforms, warmth levels, and relevance scores.

4. **Dropped signals** — Use `get_dropped_signals` (2-3 pages) to see what's being filtered out. Look for good leads being dropped vs genuine noise.

5. **Listener health** — Use `list_listeners` then `get_listener` for each to see keyword performance, rejection rates, signal rates.

6. **Brand context** — Use `get_product_info` to understand the product, ICP, and brand voice settings.

7. **Usage** — Use `get_usage` to check plan limits and consumption.

## Step 2: Calculate Funnel Metrics

Build the conversion funnel from the signal board data:

```
Total Signals → Reached Out → Replied → Won
     [N]           [N]          [N]      [N]
         [X%]          [X%]        [X%]
```

Calculate these rates:
- **Action Rate** — signals acted on (reached_out + replied + won + lost + no_response) / total signals
- **Outreach Rate** — reached_out / (total - ignored)
- **Reply Rate** — replied / reached_out
- **Win Rate** — won / replied
- **Loss Rate** — (lost + no_response) / reached_out
- **Ignore Rate** — ignored / total signals

Also calculate:
- **Average relevance score** of won signals vs lost/ignored signals
- **Platform breakdown** — which platforms generate the most actionable signals
- **Intent breakdown** — which intent types convert best
- **Warmth distribution** — hot/warm/cool ratio

## Step 3: Diagnose the Broken Lever

There are three levers. Diagnose which one (or more) is the problem:

### Lever 1: Targeting (Signal Quality)
**Symptoms:**
- Ignore rate > 50% — most signals aren't worth acting on
- Action rate < 30% — signals pile up untouched
- Average relevance score < 60 on actioned signals
- Dropped signals contain good leads (AI filtering too aggressively)
- High volume but low warmth (mostly cool signals)

**Root causes to investigate:**
- Keywords too broad (check keyword signal rates in listener data)
- Wrong platforms (audience isn't on the monitored platforms)
- Threshold too low (letting noise through) or too high (dropping good leads)
- ICP misconfigured (attracting wrong personas)
- Missing negative keywords (known noise sources not blocked)

**Prescription:** Recommend `/listener-tune` with specific keyword changes. Reference the keyword performance data.

### Lever 2: Voice (Outreach Quality)
**Symptoms:**
- Outreach rate is fine (>40%) but reply rate < 10%
- Signals are relevant but responses don't generate engagement
- No_response rate > 70% of reached_out signals

**Root causes to investigate:**
- Brand voice not configured (check `get_product_info` voice settings)
- Messages too generic (not referencing the specific post/problem)
- Too salesy on first touch (pitching before building rapport)
- Wrong tone for platform (LinkedIn formality on Reddit, or vice versa)
- Response style mismatch (witty when they need helpful, etc.)

**Prescription:** Recommend `/calibrate-voice`. Show examples of what the current voice produces vs what might work better. Suggest specific `doNots` and `customInstructions` changes.

### Lever 3: Reply Handling (Conversion Quality)
**Symptoms:**
- Reply rate is decent (>15%) but win rate < 20%
- Lots of `replied` signals that never move to `won`
- Signals stuck in `replied` status for weeks (no follow-up)

**Root causes to investigate:**
- No follow-up process (replied signals go stale)
- Not qualifying replies (treating "cool" the same as "let's talk")
- Missing the conversion moment (not suggesting next step when prospect is interested)
- Too slow to respond (hot leads go cold)

**Prescription:** Recommend `/handle-replies` for systematic reply processing. Suggest a daily reply-checking routine.

## Step 4: Present the Diagnosis

Structure your output as:

### Funnel Overview
Show the conversion funnel with actual numbers and rates.

### Diagnosis
State which lever(s) are broken and why, with evidence:
- "Your **targeting** is the bottleneck. 62% of signals are being ignored, and keyword 'project management' has a 0.3% signal rate across 847 matches."
- "Your **voice** needs work. You're reaching out to 45% of signals but only getting 6% reply rate. Brand voice isn't configured — responses are using generic defaults."

### What's Working
Call out what IS working. Protect it:
- "Your competitor keywords ('alternative to Notion') are generating 8% signal rate — keep these."
- "Reddit signals convert 3x better than X signals for your product."

### Stop / Start / Continue
Concrete changes:

| Action | Details |
|--------|---------|
| **Stop** | Using keyword "productivity tool" — 0.2% signal rate, 400+ noise matches |
| **Start** | Configuring brand voice — currently using generic defaults |
| **Start** | Processing replied signals daily — 12 are sitting in replied with no follow-up |
| **Continue** | Competitor keyword strategy — 3 of your 5 wins came from competitor-intent signals |

### Recommended Next Steps
Link to specific skills:
- "Run `/listener-tune` to fix the targeting issue"
- "Run `/calibrate-voice` to set up your brand voice"
- "Run `/handle-replies` to process the 12 stale replied signals"

## Guidelines

- **Numbers first, opinions second.** Every diagnosis must cite specific metrics. "Reply rate is low" is useless. "Reply rate is 6% (3 replies from 48 reached_out)" is actionable.
- **Don't blame the user.** Frame issues as system optimization, not personal failure. "The default voice settings aren't calibrated to your style" not "your messages are bad."
- **One primary diagnosis.** Even if multiple levers need work, identify the biggest bottleneck first. Fix the bottleneck before optimizing elsewhere.
- **Celebrate wins.** If they have won deals, call out what made those signals convert. The pattern of what works is as valuable as knowing what's broken.
- **Be honest about data limitations.** If there aren't enough reached_out or replied signals to calculate meaningful rates, say so: "With only 5 outreach attempts, reply rate isn't statistically meaningful yet. Focus on increasing outreach volume first."
- **Time context matters.** A workspace that's been active for 2 days needs different advice than one that's been running for 2 months. Check signal dates.
