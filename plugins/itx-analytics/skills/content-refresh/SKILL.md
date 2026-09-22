---
name: content-refresh
description: Find the pages worth updating — content that lost search traffic or engagement, and evergreen pages that keep earning. Use when the user asks what to update, which posts are decaying, or wants a content plan grounded in analytics.
---

# Content to refresh

## Steps

1. Two `query` calls on the search channel, same length, back to back:
   ```
   metrics: ["views","visitors","avg_time","bounce_rate"]
   dimensions: ["page"]
   filters: {ref_type: "search"}
   sort: "views desc", limit: 100
   ```
   once for the period (default last 90 days) and once for the period before it. Join on page.
2. Rank by absolute decline in search views; keep pages that still have meaningful volume (ignore pages under ~50 views in the earlier period unless the user is small).
3. For each of the top candidates (Pro): `get_page_report` for the trend and scroll depth, `get_page_search_queries` for the queries it still ranks for (and which are fading).
4. Also list the **evergreen winners** — top pages whose views are flat or rising over 6 months (`query` with `dimensions: ["month","page"]` for the top 20 pages) — they deserve internal links from the refreshed pages.
5. Pro: `get_content` gives publish date, author, word count, decay and evergreen scores per post; use it to spot old, thin posts.

## Output

A prioritized table:

| Page | Search views before → after | Change | Likely reason | Suggested update |
|---|---|---|---|---|

Reasons should be specific: "queries about X fell, the page still targets the 2023 version", "position dropped from 4 to 9 for its main query", "scroll depth stops at 40%, the answer is below the fold".

End with three quick wins and one structural suggestion (a hub page, internal links, consolidation).
