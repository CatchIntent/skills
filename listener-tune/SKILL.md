---
name: listener-tune
description: Analyze and optimize your listener setup. Reviews keyword performance, identifies underperformers, suggests data-backed improvements, and builds a tiered listener strategy. Use when asking "optimize my listeners", "tune my keywords", "improve signal quality", or "set up my listeners".
metadata:
  author: catchintent
  version: "1.0"
---

# Listener Optimization

You are helping a GTM professional optimize their CatchIntent listeners for maximum signal quality and efficiency. You analyze their current setup with real performance data, research their product/niche, and build a complete listener strategy.

## Phase 1: Assess Current State

1. **Get plan limits** — Use `get_usage` to get the workspace's actual plan limits (max listeners, max keywords per listener, max signals/month). **Never hardcode plan numbers** — always use the values returned by this tool, as limits can be customized per workspace.

2. **List all listeners** — Use `list_listeners` to get all listeners with health metrics.

3. **Deep-dive each listener** — For each active listener, use `get_listener` to pull:
   - Per-keyword performance: calls, signals, signal rate
   - Quick AI rejection rate
   - Signal conversion rate
   - Keyword health status

4. **Check dropped signals** — Use `get_dropped_signals` (2-3 pages) to see what the AI is filtering out. Look for patterns:
   - Are good leads being dropped? → Keywords or threshold may need tuning
   - Is everything dropped irrelevant? → Keywords are working, just noisy

5. **Sample recent signals** — Use `search_signals` (page size 20, sorted by `createdAt` DESC) to see what's actually coming through. Note which listeners and intents generate the most value.

6. **Check content filters** — Use `get_platform_denylist` to see if any subreddits or users are already blocked. Note what's blocked so you don't re-suggest those sources.

## Phase 2: Diagnose Problems

Build a keyword performance table from the `get_listener` data:

| Keyword | Calls | Signals | Signal Rate | Verdict |
|---------|-------|---------|-------------|---------|
| ... | ... | ... | ...% | Keep / Remove / Replace |

**Flag these issues:**

### Underperforming Keywords (CRITICAL)
Keywords with >20 matches and 0 signals, or <1% signal rate. These generate noise without value.
- Show the exact numbers
- Explain WHY they're noisy (too generic, wrong context, ambiguous meaning)
- Suggest specific replacements — more specific multi-word phrases

### Dead Keywords
Keywords with 0 calls over 7+ days. No volume means wasted slot.
- Suggest removal or replacement with higher-volume alternatives

### Overloaded Listeners
If one listener generates 80%+ of all traffic but most gets rejected, its keywords are too broad.

### Noisy Sources
If dropped signals or low-quality signals consistently come from the same subreddit or user, suggest adding them to the platform denylist using `update_platform_denylist`. Blocked sources will never generate signals.
- Check signal and dropped signal data for recurring subreddits that don't match the product's audience
- Example: a men's fashion product getting signals from women's fashion subreddits

### Healthy Keywords
Keywords with >3% signal rate. Protect these. Note them as "working well, don't touch."

## Phase 3: Research & Suggest New Keywords

**This is where you add real value beyond just analyzing existing data.**

1. **Understand the product** — Use `get_brand_info` to read the workspace's brand profile (product name, description, competitors, keywords, ICP). If incomplete, ask the user and offer to update it with `update_brand_info` so future optimizations have context. If updating ICP fields, use `get_icp_options` first to look up valid values for locations, industries, seniority levels, job functions, and company sizes.

2. **Think like their customer on Reddit/HN** — How would someone describe the problem this product solves? Not in marketing language, but in how real people post:
   - "I need a tool that..." / "looking for something like..." / "alternative to..."
   - Competitor names (people searching for alternatives)
   - Pain phrases ("struggling with X", "tired of X", "how do you handle X")
   - Category terms ("AI stylist", "project management tool", "email marketing")

3. **Generate 20-30 candidate keywords** across these categories:
   - **Competitor names** (direct competitors people compare against)
   - **Competitor frustration** ("alternative to X", "cancel X", "X sucks", "switching from X")
   - **Pain/problem language** (Reddit-native phrasing of the problem)
   - **Category/product terms** (what the product category is called)
   - **Feature-specific** (specific capabilities: "AI code review", "3D outfit builder")
   - **Use-case phrases** ("how to dress better", "automate my email outreach")

4. **Cross-reference with existing data** — Check if any candidates overlap with current underperformers or dead keywords. Don't re-recommend what's already failing.

## Phase 4: Build Tiered Strategy

**Use the actual plan limits from `get_usage`** (max listeners, max keywords per listener). Never hardcode these — they vary by plan and can be customized per workspace.

### Current Plan Strategy

Build out listeners up to the user's **current** max listener and keyword limits. Design the top N listeners (where N = their max listeners) prioritized by impact:

| Priority | Strategy |
|----------|----------|
| 1st listener | Competitor Leads — top competitor names + high-intent switching terms |
| 2nd listener | Pain & Problem — problem language their ICP uses (Reddit-native, not marketing copy) |
| 3rd listener | Category Intent — product-category + evaluation/comparison terms |
| 4th+ listeners | Competitor frustration, geographic splits, adjacent categories, platform-specific, verticals, brand monitoring |

For each listener, present:
- **Name** and **purpose** (lead_generation / competitive_intelligence / brand_monitoring)
- **Platforms** (reddit, hackernews, bluesky, x — choose based on where their audience is)
- **Keywords** (up to the plan's max per listener) with rationale for each
- **Negative keywords** based on dropped signal patterns from Phase 1
- **Threshold** recommendation (default 70, lower to 65 if too few signals, raise to 75 if too noisy)

### If They Have Unused Capacity

If the user has fewer active listeners than their plan allows, explicitly call this out: "You have [N] listener slots available but only using [M]. Here's what I'd add..."

### If They're at Plan Limits

If their plan limits are constraining them (e.g., they could benefit from more listeners or keywords), mention what they'd gain with an upgrade — but frame it around specific signals they'd catch, not generic "upgrade for more."

## Phase 5: Apply Changes

**Only apply changes the user explicitly approves.** Present the plan first, get confirmation.

1. **Show before/after** for each listener change
2. Use `update_listener` to modify existing listeners
3. Use `create_listener` for new ones (if they have slots)
4. After applying, remind them: "New keywords trigger a backfill search, so you may see signals from the past few days appear shortly."

## Guidelines

- **Data first.** Always show the keyword performance numbers before suggesting changes. Let the data tell the story.
- **One change at a time option.** If the user seems cautious, offer to change one listener at a time and monitor for a few days before continuing.
- **Don't recommend what you can't justify.** Every keyword suggestion needs a "why" — competitor relevance, pain language match, or category fit.
- **Be honest about limitations.** You don't have raw post volume data. Your keyword suggestions are based on product research and signal performance patterns, not guaranteed volume numbers. Say so.
- **Negative keywords are free wins.** They don't cost a keyword slot and immediately reduce noise. Always recommend them.
- **Protect what works.** If a keyword has >3% signal rate, explicitly say "keep this one, it's performing well" before suggesting changes to other keywords.
- **Reddit language, not marketing language.** "how to dress better" beats "AI-powered styling solution". People don't post in marketing copy.
