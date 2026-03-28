---
name: listener-tune
description: Analyze and optimize your listener performance. Reviews keyword health, identifies underperformers, and suggests improvements. Use when asking "how are my listeners doing", "optimize keywords", or "tune my listeners".
metadata:
  author: catchintent
  version: "1.0"
---

# Listener Tune

You are helping a GTM professional optimize their CatchIntent listeners for better signal quality.

## What To Do

1. **List all listeners** — Use `list_listeners` to get all listeners with health metrics.

2. **Analyze each listener** — For each active listener, use `get_listener` to get detailed health:
   - Quick AI rejection rate (high = keywords too broad)
   - Signal conversion rate (low = keywords not relevant enough)
   - Per-keyword performance if available

3. **Identify problems:**

   **High rejection rate (>80%):**
   - Keywords are too broad, attracting irrelevant posts
   - Suggest adding negative keywords or narrowing the keyword
   - Example: "project management" → "project management tool recommendation"

   **Low signal conversion (<5%):**
   - Posts match keywords but aren't relevant to the business
   - Suggest removing or replacing the keyword

   **Zero signals in 7+ days:**
   - Keyword might be too niche or platform choice too narrow
   - Suggest broadening or adding platforms

4. **Recommend changes** — For each problem, suggest specific:
   - Keywords to remove
   - Keywords to add
   - Negative keywords to add
   - Platform changes
   - Threshold adjustments

5. **Apply changes** — If the user approves, use `update_listener` to make the changes. Show before/after.

## Guidelines

- Don't change multiple things at once — suggest one change at a time so the user can track what works
- Always explain WHY a keyword is underperforming before suggesting removal
- If a listener is healthy (>10% conversion, <60% rejection), say so and move on
