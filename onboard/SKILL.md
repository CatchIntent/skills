---
name: onboard
description: Guided first-run setup for new CatchIntent workspaces. Analyzes your website, auto-suggests ICP and competitors, configures brand voice defaults, and creates your first listener. Use when setting up CatchIntent for the first time, or when brand info is empty.
compatibility: Requires CatchIntent MCP server connected via claude mcp add
metadata:
  author: catchintent
  version: "1.0"
---

# Workspace Onboarding

You are helping a new CatchIntent user set up their workspace from scratch. The goal is to go from zero to a fully configured workspace with brand info, ICP, and a first listener — in a single conversation.

## Step 1: Check Current State

Use `get_brand_info` to see what's already configured. If the workspace already has a name, description, ICP, and at least one listener (`list_listeners`), tell the user they're already set up and offer to run `/listener-tune` or `/calibrate-voice` instead.

If brand info is mostly empty, proceed with onboarding.

## Step 2: Understand the Product

Ask the user for their website URL. Fetch it and extract:
- **Product name** and one-line description
- **What it does** — the core value prop in plain language
- **Who it's for** — target audience from the page copy
- **Competitors** mentioned or implied (pricing pages often list them)
- **Key features** that differentiate

If the website doesn't have enough info, ask the user directly: "What does your product do, and who's your ideal customer?"

## Step 3: Configure Brand Info

Use `update_brand_info` to set:
- `name` — product name
- `description` — concise description of what the product does
- `domain` — website domain
- `keywords` — 5-10 product-related keywords (product name, category terms, key features)
- `competitors` — competitor names discovered from the website or user input

Present what you're about to save and confirm before applying.

## Step 4: Configure ICP

Use `get_icp_options` to get valid values for each ICP field. Then suggest:
- `targetRoles` — 3-5 job titles most likely to buy (based on the product's audience)
- `industries` — 3-5 industries that match the product's use case
- `seniorityLevels` — appropriate seniority (e.g., Manager+ for B2B tools)
- `companySizes` — reasonable company size ranges
- `locations` — suggest starting broad (e.g., United States, United Kingdom) unless the product is geo-specific

Apply via `update_brand_info` with the `icp` field after user confirms.

## Step 5: Set Default Brand Voice

Set sensible defaults via `update_brand_info`:
- `voice.tone` — suggest `friendly` for most B2B SaaS, `casual` for consumer/dev tools
- `voice.personality` — suggest 2-3 traits based on the website's tone (e.g., `["helpful", "knowledgeable"]`)
- `voice.doNots` — set `["Don't pitch the product in first-touch messages", "Don't use marketing buzzwords"]` as defaults
- `voice.customInstructions` — "Lead with value. Reference the specific problem or question the person raised. Keep it conversational."

Tell the user they can refine this later with `/calibrate-voice` using real message examples.

## Step 6: Create First Listener

Based on the product and competitors, create the first listener using `create_listener`:
- **Name** — "[Product] — Competitor Leads" (competitor names are usually the highest-signal keywords)
- **Purpose** — `lead_generation`
- **Platforms** — `["reddit", "hackernews"]` as default (broadest intent coverage), add `x` or `bluesky` if relevant
- **Keywords** — 5-8 keywords: top 2-3 competitor names + 2-3 pain/problem phrases + 1-2 category terms
- **Threshold** — 70 (default)

Explain what each keyword will catch and why you chose it. Use Reddit-native language for pain phrases, not marketing copy.

## Step 7: Summary & Next Steps

Present a summary:
- Brand info configured
- ICP set
- First listener active
- Expected: "You should start seeing signals within a few hours as the listener processes recent posts."

Suggest next steps:
- `/morning-signals` — check signals tomorrow morning
- `/calibrate-voice` — refine brand voice with real message examples
- `/listener-tune` — optimize keywords after 3-5 days of data

## Guidelines

- **Don't overwhelm.** One listener is enough to start. More can be added after they see data.
- **Explain your reasoning.** For every ICP field and keyword, say why you chose it.
- **Use get_icp_options.** Never guess valid values for ICP fields — always look them up.
- **Reddit language, not marketing language.** Keywords should match how people actually post: "alternative to Slack" not "enterprise communication platform".
- **Confirm before applying.** Always show what you're about to save and wait for approval.
