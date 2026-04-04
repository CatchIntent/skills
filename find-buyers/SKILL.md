---
name: find-buyers
description: Find people showing active buying behavior on LinkedIn — commenting about problems, discussing solutions in groups, and companies hiring for relevant roles. Use when you want to "find buying signals", "find people with intent", "who's looking for a solution like ours", or "find active buyers".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Find Buyers

You are helping a GTM professional find people showing active buying intent on LinkedIn. Unlike prospecting by title/industry, this workflow finds people based on what they're actually doing — commenting on relevant posts, discussing problems in groups, and working at companies that are hiring.

## What To Do

### Step 1: Establish Context

1. **Check usage and credits** — Use `get_usage` to check agentic search credit balance (`agenticSearchCredits.balance`). Full workflow costs ~13 credits. Warn if balance is low.
2. **Get brand context** — Use `get_brand_info` to understand the product, ICP, and competitors. This shapes keyword selection and result interpretation.
3. **Define the topic** — The user must provide a topic, problem area, or industry keyword. Help them refine:
   - Too broad: "sales" → suggest "sales automation", "SDR outreach", "sales engagement platform"
   - Too narrow: "CatchIntent alternative" → suggest also searching the category term
   - Good: "social listening for B2B", "intent data", "SDR productivity"

   If the user isn't sure, derive keywords from `get_brand_info` — product description, competitor names, and ICP pain points are good starting points.

### Step 2: Find High-Intent Commenters (~5 credits)

Run `find_comments` with the user's keyword. This is the highest signal-to-cost tool — it searches LinkedIn posts about the topic, fetches all commenters, and uses AI to classify each by intent:

- Pain expression ("struggling with...", "our current tool doesn't...")
- Solution seeking ("has anyone tried...", "looking for recommendations...")
- Switching intent ("thinking about moving away from...")
- Alternatives comparison ("how does X compare to Y?")

Present classified commenters:

| Name | Title | Intent Score | What They Said | Post Context |
|------|-------|-------------|----------------|--------------|
| ... | VP Marketing | 85 | "We've been evaluating tools for this exact use case..." | Post about B2B intent data |

These are your warmest leads. They're not just matching a title — they literally wrote about needing what you sell.

### Step 3: Find Group Discussions (5 credits)

Run `find_group_prospects` with the same search term. LinkedIn groups surface more direct buying discussions than the feed — people ask "has anyone used X for Y?" or "what tool do you recommend for Z?"

Present findings:
- Groups scanned (name, member count)
- High-intent commenters found
- Top prospects with their discussion context

| Name | Title | Group | What They Asked |
|------|-------|-------|-----------------|
| ... | ... | B2B Growth | "Best tools for tracking competitor mentions on Reddit?" |

### Step 4: Find Hiring Signals (3 credits)

Run `find_hiring_signals` with relevant job titles derived from the topic:
- If topic is "sales automation" → search for ["SDR", "Sales Development", "RevOps"]
- If topic is "social listening" → search for ["Social Media Manager", "Community Manager", "Growth Marketing"]
- If topic is "intent data" → search for ["Demand Gen", "Marketing Ops", "Growth"]

Hiring = building a function = buying tools to support that function. Present:

| Company | Open Roles | Hiring Pattern | Industry |
|---------|-----------|----------------|----------|
| ... | 3 SDR roles, 1 VP Sales | Scaling outbound | SaaS |

### Step 5: Cross-Reference with Signals (free)

Run `search_signals` with the topic keyword. Show any Reddit/HN/X/Bluesky discussions — these complement the LinkedIn signals with multi-channel intent.

### Step 6: Present Summary

```
## Buying Signal Report: [Topic]

### High-Intent Commenters
- X people found actively discussing [topic] on LinkedIn
- Top intents: [pain expression, solution seeking, etc.]
- Best lead: [Name] — "[their comment]"

### Group Discussions
- Scanned X groups (Y total members)
- Z high-intent participants imported

### Companies Hiring
- X companies actively hiring for [roles]
- Notable: [Company] scaling outbound team (3 SDR roles)

### Cross-Channel Signals
- X Reddit/HN/X discussions about [topic]
- Notable: [any hot signals]

### Total Imported: X new prospects
### Credits Used: ~13

### Recommended Next Steps
...
```

### Step 7: Suggest Follow-ups

- **"Want more volume?"** → `find_keyword_engagers` with the same keyword (~10 credits). Captures reactions + comments, broader but lower intent. "This finds people who reacted to these same posts — more volume, but they liked a post rather than wrote about a problem."
- **"Deep-dive the top prospects?"** → `/research-prospect` for behavioral intelligence on the most promising people (2 credits each)
- **"Research the hiring companies?"** → `/research-competitor` on the top hiring companies
- **"Draft outreach for the warm leads?"** → Reference their specific comments in personalized messages

## Guidelines

- Always run find_comments first — it has the best signal-to-cost ratio
- Help the user pick good keywords. Vague keywords waste credits on irrelevant results. Specific problem language works best.
- Do NOT run `find_keyword_engagers` as part of the default workflow — it overlaps with `find_comments` (same posts, same keyword search) but captures reactions instead of comments, at 2x the cost. Offer it as a follow-up for more volume.
- For hiring signals, translate the user's topic into job titles. "Sales automation" → SDR/BDR/RevOps roles, not "Sales Automation Specialist".
- Cross-reference with signals is free — always do it. Multi-channel intent (LinkedIn comment + Reddit post) is a strong buying signal.
- When presenting commenters, always show what they said. The comment IS the intent signal. "VP Marketing, Intent Score 85" means nothing without "We've been evaluating tools for this exact use case."
- If results are thin for a keyword, suggest related terms or broader category words before giving up.
