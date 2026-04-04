---
name: research-prospect
description: Get deep behavioral intelligence on a specific LinkedIn prospect — what they engage with, what topics they care about, and how they match your ICP. Use when you want to "research someone", "check this prospect", "what does this person care about", or before an important outreach.
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Research Prospect

You are helping a GTM professional get deep behavioral intelligence on a specific person before outreach or a meeting. This goes beyond profile data — it analyzes what they actually engage with on LinkedIn to reveal what they care about.

## What To Do

### Step 1: Identify the Person

The user provides either:
- **A LinkedIn URL** (e.g., linkedin.com/in/janedoe) — use directly
- **A name** — Use `search_people` to look them up in the workspace first. If found, use their LinkedIn URL. If not found, ask the user for the LinkedIn URL.

### Step 2: Check Credits and Run Deep Dive

1. **Check credits** — Use `get_usage` to check agentic search credit balance (`agenticSearchCredits.balance`). This costs 2 credits.
2. **Run the analysis** — Use `prospect_deep_dive` with the LinkedIn URL (and name if available).

### Step 3: Present the Behavioral Brief

Structure the output as a profile intelligence brief:

```
## [Name] — Behavioral Intelligence

### Profile
- Title: [current title]
- Company: [company]
- LinkedIn: [url]

### What They Engage With
- Topics: [list of topics they comment on / react to]
- Comment count: [X comments analyzed]
- Sample comments:
  - "[quote]" (on a post about [topic])
  - "[quote]" (on a post about [topic])

### ICP Relevance
- Score: [X/100]
- Reasoning: [AI explanation of why they match or don't match]

### Outreach Angle
Based on their engagement patterns, here's what would resonate:
- They care about [topic] — reference this in your opener
- They're active in discussions about [problem area]
- They recently engaged with content about [specific thing]
```

### Step 4: Check for Interest Patterns (optional, +2 credits)

If the user wants deeper insight, offer to run `check_person_interests` for engagement history:
- Top companies they engage with (who influences them?)
- Recent reaction patterns (what content catches their eye?)
- Volume of activity (are they an active engager or lurker?)

### Step 5: Cross-Reference with Signals (free)

Run `search_signals` with the person's company name to check for any Reddit/HN/X/Bluesky discussions involving their company. This adds multi-channel context:

- "Their company was mentioned in 3 Reddit threads about [topic] this month"
- "Someone at their company posted on HN asking about [problem]"

### Step 6: Suggest Next Steps

Based on the analysis:
- **High ICP match + active engager** → "Ready to reach out? I can draft a message referencing their comments about [topic]."
- **High ICP match + passive** → "They match your ICP but don't engage much publicly. Try connecting on LinkedIn with a note about [their company's challenge]."
- **Low ICP match** → "They don't match your ICP strongly — [reasoning]. Want to research someone else?"
- **Interesting company** → "Their company looks interesting — want me to research who else is there?" (run `/find-prospects` with `currentCompanies` filter)

## Guidelines

- This skill is most valuable BEFORE important outreach — use it to craft highly personalized messages
- Always present the behavioral data, not just the score. The score is a summary; the comments and topics are the actionable intel.
- Profile comments are the secret weapon — someone who comments on attribution posts but never posts about attribution is still thinking about attribution. Call this out explicitly.
- If the person isn't in the workspace yet, the deep dive still works and will import them. Mention this.
- Cross-reference with signals is free — always do it. Multi-channel context (LinkedIn behavior + Reddit/HN mentions at their company) makes outreach incredibly specific.
- If the deep dive returns no activity ("Could not analyze this profile"), the person likely has a private profile or no public engagement. Suggest trying `check_person_interests` for reaction data, or move on to another prospect.
- When suggesting outreach angles, be specific. Don't say "reference their interests." Say "They commented on 3 posts about SDR productivity tools — open with the pain point they described: '[exact quote]'."
