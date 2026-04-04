# Skill Standards

All CatchIntent skills follow these conventions. Read this before writing or modifying any skill.

## Frontmatter (agentskills.io spec)

Every `SKILL.md` must have:

```yaml
---
name: skill-name          # lowercase + hyphens, must match directory name
description: ...          # under 1024 chars, describes what AND when to use
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---
```

## Interaction Protocol

### Data Before Opinions
Always fetch and show real data before making recommendations. Never suggest changes based on assumptions — cite specific numbers from tool results.

### Confirm Before Writing
Before calling any tool that modifies state (`update_brand_info`, `update_listener`, `create_listener`, `update_signal_status`, `update_platform_denylist`, `update_person_status`), show what you're about to change and wait for explicit user approval.

Read-only tools (`get_*`, `search_*`, `list_*`) can be called without confirmation.

### One Thing at a Time
When processing multiple items (signals, replies, listeners), handle each fully before moving to the next. Don't batch approvals — let the user confirm or adjust each one.

### Show Your Reasoning
When classifying, scoring, or recommending, explain why. Quote specific data: "Keyword 'project management' has 0.3% signal rate across 847 matches — that's noise." Don't just label things.

## Tool Usage Patterns

### Always Start With Context
Most skills should begin with `get_brand_info` to understand the product, competitors, ICP, and brand voice. This context shapes everything — prioritization, language, recommendations.

### Check Plan Limits
Before suggesting features or actions, use `get_usage` to check what the user's plan supports. Never recommend features they can't access. Never hardcode plan limits — always read them from the tool response.

### Funnel Awareness
When analyzing performance, use `get_workspace_stats` which returns a `funnel` object with conversion rates:
- `actionRate` — % of signals acted on (reached_out / total)
- `replyRate` — % of outreach that got replies (replied / reached_out)
- `winRate` — % of replies that converted (won / replied)

Use these rates to diagnose bottlenecks rather than raw counts alone.

### First-Touch Outreach
When generating DMs via `generate_response`, pass `firstTouch: true` (the default) for initial outreach. This enforces: no product pitch, lead with the signal, value-first. Only set `firstTouch: false` for follow-up messages where product mention is appropriate.

### Platform Denylist
If you notice recurring low-quality sources (subreddits, users), proactively suggest blocking them via `update_platform_denylist`. Mention this is free and immediate.

## Output Style

- **Scannable.** Tables for lists, bullet points for recommendations. No walls of text.
- **Numbers first.** Lead with data, then interpretation.
- **Actionable.** End with specific next steps, not vague advice. Link to other skills when relevant (e.g., "Run `/listener-tune` to fix keyword targeting").
- **Honest about limits.** If there isn't enough data to draw conclusions, say so. "With only 5 outreach attempts, reply rate isn't meaningful yet" is better than a false diagnosis.

## Agentic Search Tool Patterns

Skills that use LinkedIn prospecting tools (`check_credits`, `lookup_filters`, `execute_search`, `find_comments`, `find_competitor_engagers`, `find_profile_engagers`, `find_keyword_engagers`, `find_group_prospects`, `find_hiring_signals`, `browse_company_posts`, `analyze_competitor_ads`, `check_person_interests`, `prospect_deep_dive`) must follow these patterns:

### Always Check Credits First
Before any credit-consuming tool, check the user's agentic search credit balance via `get_usage` (the `agenticSearchCredits.balance` field). Warn the user if their balance is insufficient. Show the estimated cost before executing. You can also call `check_credits` for a dedicated credit check, but `get_usage` is preferred since it returns plan limits and signal usage alongside credits — one call instead of two.

### Use lookup_filters for IDs
Never hardcode LinkedIn filter IDs (geography, industry, seniority, etc.). Always use `lookup_filters` to resolve human-readable names to IDs at runtime.

### Confirm Before Spending Credits
Unlike read-only tools, agentic search tools consume credits. Always show what you're about to do and the estimated cost, then wait for confirmation before executing.

### Comment Content Is the Hook
When tools return commenters (`find_comments`, `find_group_prospects`), always display what they said. The comment is the personalization hook for outreach — a name and title without context is useless.

### Cross-Reference with Signals
After any LinkedIn prospecting, run `search_signals` (free) to cross-reference imported people's companies against Reddit/HN/X/Bluesky discussions. Multi-channel intent is a powerful signal.

### Avoid Duplicate API Calls
`find_comments` and `find_keyword_engagers` search the same posts for the same keyword. Never run both in a single workflow — lead with `find_comments` (higher intent, lower cost) and offer `find_keyword_engagers` as a follow-up for more volume.

### Credit Cost Awareness
Tool costs are dynamic and derived from constants — descriptions include the current cost. When presenting workflow estimates, add up the individual tool costs rather than hardcoding a total.

## Cross-Skill References

Skills should reference each other when relevant:

| Situation | Suggest |
|-----------|---------|
| Brand voice empty or generic | `/calibrate-voice` |
| Keywords underperforming | `/listener-tune` |
| Signals piling up unactioned | `/draft-outreach` |
| Replies sitting unprocessed | `/handle-replies` |
| Low conversion rates | `/campaign-retro` |
| ICP not configured | `/calibrate-voice` (includes ICP setup) |
| Want to find LinkedIn prospects | `/find-prospects` |
| Competitive intelligence needed | `/research-competitor` |
| Looking for active buyers on LinkedIn | `/find-buyers` |
| Need to research a specific person | `/research-prospect` |
| Interesting LinkedIn post to mine | `/analyze-post` |

## Signal-First Personalization

When drafting any outreach (in `/draft-outreach`, `/handle-replies`, or any skill that generates messages), always lead with the signal — the specific post, comment, or activity that surfaced this person. This is what makes CatchIntent outreach different from generic cold outreach. The signal IS the personalization hook.

Bad: "I noticed you're interested in sales automation"
Good: "Saw your Reddit post about struggling with SDR outreach at scale"
