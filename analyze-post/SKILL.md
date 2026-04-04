---
name: analyze-post
description: Analyze a LinkedIn post to find high-intent commenters — people expressing pain, seeking solutions, or evaluating tools. Paste any LinkedIn post URL and get qualified prospects in seconds. Use when you want to "analyze this post", "find prospects from this post", "who's interested in this thread", or "check the comments on this".
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Analyze Post

You are helping a GTM professional extract qualified prospects from a specific LinkedIn post. This is one of CatchIntent's highest-value, lowest-cost operations: paste a URL, get every high-intent commenter identified, classified, and imported.

## What To Do

### Step 1: Get the Post URL

The user provides a LinkedIn post URL. Accept these formats:
- `https://www.linkedin.com/posts/username_topic-activity-...`
- `https://www.linkedin.com/feed/update/urn:li:activity:...`
- `https://www.linkedin.com/pulse/...`

If the user describes a post instead of providing a URL ("that viral post about SDR burnout"), you can't look it up — ask them to paste the URL directly.

### Step 2: Check Credits and Analyze

1. **Check credits** — Use `get_usage` to check agentic search credit balance (`agenticSearchCredits.balance`). Analyzing a single post costs ~1 credit.
2. **Get brand context** — Use `get_brand_info` so the AI classifier knows your product and ICP when scoring intent.
3. **Run the analysis** — Use `find_comments` with `postUrl` set to the user's URL. This:
   - Fetches all comments on the post (including replies to comments)
   - Uses AI to classify each commenter by intent: pain expression, solution seeking, switching intent, alternatives comparison, frustration
   - Filters to high-intent commenters (score >= 50)
   - Imports them as prospects with discovery context

### Step 3: Present Results

Show the post analysis:

```
## Post Analysis

### Post
[Brief description of the post topic based on context clues]

### High-Intent Commenters Found: X

| Name | Title | Intent Score | Intent Type | What They Said |
|------|-------|-------------|-------------|----------------|
| ... | VP Marketing | 92 | Solution seeking | "We've been looking for exactly this..." |
| ... | Head of Growth | 78 | Pain expression | "This is our biggest challenge right now" |
| ... | Founder | 71 | Switching intent | "Our current tool doesn't handle this well" |

### Summary
- Total comments analyzed: X
- High-intent commenters: Y (Z% of total)
- Imported: A new prospects
- Already tracked: B
- Credits used: ~1
```

### Step 4: Cross-Reference (free)

Run `search_signals` to check if any of these commenters' companies appear in your Reddit/HN/X/Bluesky signals. Multi-channel intent is powerful:

"[Name]'s company also appeared in 2 Reddit threads about [topic] this week — strong multi-channel buying signal."

### Step 5: Suggest Next Steps

Based on the results:
- **High-intent commenters found** → "Want me to deep-dive the top 3?" → `/research-prospect` (2 credits each) for full behavioral profiles
- **Competitor post analyzed** → "Want full competitive intelligence on [company]?" → `/research-competitor`
- **Topic resonating** → "Want to find more people discussing [topic]?" → `/find-buyers` with the post's topic as keyword
- **Ready to reach out** → "I can draft personalized outreach referencing their specific comments"

## Use Cases

Guide the user to understand when this skill is most powerful:

1. **Viral post about a problem you solve** — Someone posts about a pain point in your space and it goes viral. Paste the URL → find every prospect in the comments who's experiencing that pain.

2. **Competitor content** — Your competitor publishes a product update or thought piece. Paste the URL → find people engaging with it, especially frustrated commenters.

3. **Industry thought leader** — A prominent voice posts about a trend relevant to your product. Paste the URL → find practitioners responding with real experience.

4. **Your own posts** — You published content and got engagement. Paste the URL → identify which engagers match your ICP and are worth reaching out to.

5. **Event/conference posts** — Someone posts a takeaway from an industry event. Paste the URL → find attendees discussing the topic.

## Guidelines

- This is the cheapest prospecting tool (~1 credit). Encourage frequent use — "See an interesting post? Paste it."
- Always show what each commenter said. The comment is the personalization hook for outreach. A name and title without context is useless.
- Intent classification is done by AI, not keyword matching. Trust the scores but show the reasoning — the user should see WHY someone was classified as high-intent.
- If no high-intent commenters are found, it's not a failure — the post may not be attracting buyers. Suggest trying a different post or switching to `/find-buyers` for keyword-based search.
- When cross-referencing with signals, only mention it if there are actual matches. Don't show "0 signals found" — just skip it.
- If the user pastes an invalid URL, explain the expected format. Don't try to search for the post by keyword — that's a different workflow.
