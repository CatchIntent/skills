---
name: research-competitor
description: Full competitive intelligence on a LinkedIn company — their content strategy, frustrated commenters ready to switch, engaged audience, and ad messaging. Use when you want to "research a competitor", "steal competitor audience", "find people unhappy with [company]", or "competitive analysis".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Research Competitor

You are helping a GTM professional run competitive intelligence on a specific company via LinkedIn. This is a multi-step workflow ordered by value-per-credit — each step builds on the last.

## What To Do

### Step 1: Establish Context

1. **Check usage and credits** — Use `get_usage` to check agentic search credit balance (`agenticSearchCredits.balance`). Full workflow costs ~18 credits. Warn if balance is tight.
2. **Get brand context** — Use `get_brand_info` to understand the user's product, ICP, and existing competitors. This helps you interpret results and spot opportunities.
3. **Identify the competitor** — The user must provide a company name. If they say something vague like "our main competitor", check `get_brand_info` for the competitors list and confirm which one.

### Step 2: Browse Their Content (1 credit)

Run `browse_company_posts` with the competitor's name. Present findings:

- **Post frequency** — How often they publish
- **Top-performing content** — Posts with highest engagement (likes + comments)
- **Content themes** — What topics they focus on (product updates, thought leadership, case studies, hiring)
- **Engagement patterns** — Who's engaging? Customers, prospects, employees?

This gives context for everything that follows. If a competitor posts heavily about a specific pain point, their audience is primed on that topic.

### Step 3: Find Frustrated Commenters (~5 credits)

Run `find_comments` with the competitor name as `keyword`. This is the highest-value step — the AI classifier identifies commenters expressing:
- Frustration with the competitor
- Switching intent ("looking for alternatives")
- Unmet needs the competitor doesn't solve
- Negative sentiment about pricing, support, or features

Present high-intent commenters:

| Name | Title | Intent | What They Said |
|------|-------|--------|----------------|
| ... | ... | Switching | "We've been looking for something simpler than [Competitor]..." |

These are the warmest leads — people actively considering leaving. Highlight them.

### Step 4: Capture Competitor Audience (~11 credits)

Run `find_competitor_engagers` with the competitor's name. This captures people who liked or commented on the competitor's recent posts — proven interest in the space.

Present summary:
- Total engagers found
- New leads imported (after dedup)
- Already tracked (from previous research)
- Top engagers by engagement count

| Name | Title | Company | Engagements | AI Score |
|------|-------|---------|-------------|----------|
| ... | ... | ... | 4 posts | 85 |

### Step 5: Analyze Their Ads (1 credit)

Run `analyze_competitor_ads` with the competitor name. Present:

- **Active ads** — Headlines, descriptions, creative types
- **Messaging themes** — What value props they lead with
- **Positioning insights** — How they describe themselves vs how you differ

This is cheap competitive intel that informs outreach messaging — "I saw [Competitor] is pushing hard on [X], but based on your comment about [Y], it sounds like that's not solving your real problem."

### Step 6: Cross-Reference with Signals (free)

Run `search_signals` with the competitor name as `competitorMentioned` filter. Show any Reddit/HN/X/Bluesky discussions where this competitor was mentioned — these are complementary intent signals from different channels.

### Step 7: Present Summary

Compile a competitive intelligence brief:

```
## Competitive Intelligence: [Competitor Name]

### Content Strategy
- Posts X times/week, focused on [themes]
- Top post: [title] (X likes, Y comments)

### Warm Leads Found
- X high-intent commenters (frustration/switching intent)
- Y audience members imported
- Z already in your workspace

### Ad Positioning
- Leading with: [value props]
- Gap vs your product: [differentiators]

### Signal Cross-Reference
- X Reddit/HN discussions mentioning them
- Notable: [any hot signals]

### Recommended Next Steps
...
```

### Step 8: Suggest Follow-ups

- **"Want me to deep-dive the top 3 prospects?"** → `/research-prospect` for behavioral intelligence (2 credits each)
- **"Check their founder's audience?"** → `find_profile_engagers` with founder's LinkedIn URL (~10 credits). Note: founder audiences often capture VCs and peers, not just prospects. Best when the founder is a prominent thought leader.
- **"Research another competitor?"** → Re-run this workflow
- **"Ready to reach out to the warm leads?"** → Draft outreach referencing their frustration signals

## Guidelines

- Always run steps in order (browse → comments → engagers → ads) — each builds context for the next
- The frustrated commenters from Step 3 are your best leads. Highlight them prominently.
- Don't skip the ad analysis — it's only 1 credit and gives outreach messaging ammunition
- When presenting results, connect insights across steps: "3 of the frustrated commenters also engaged with their product launch post — they're evaluating actively"
- If credit balance is tight (~10), prioritize: browse (1) + comments (5) + ads (1) = 7 credits for the highest-value intel. Skip the broad engager capture.
- Cross-reference with signals is free — always do it
- Founder audience (`find_profile_engagers`) is NOT included by default because it's expensive and often low-quality. Only suggest as a follow-up.
