---
name: page-review
description: Review a single page or post — traffic trend, sources, engagement, what visitors do next, search queries — and suggest specific improvements. Use when the user names a URL or path, or asks "how is this post doing".
---

# Review one page

Take the path from the user (accept a full URL; the tools normalize it).

## Steps

1. Pro: `get_page_report` with the path and `compare: "period"`. It returns totals, trend, sources, countries, devices, scroll depth, an insight (verdict + what stands out), where visitors came from within the site, where they went next, and inbound links.
   Free: `query` with `filters: {page: "/path/"}` and dimensions `date`, `ref_type`, `country`, `device` (one query each), plus `get_events` for scroll milestones.
2. `get_page_search_queries` (Pro, search console connected) with the same dates: its queries with changes, whether its CTR is low for its position, queries close to the top 3, other pages competing for its queries, index status.
3. If the page is a landing page for a store, `get_ecommerce` (Pro) shows its sales.
4. Context: `get_pages` for the same period to place it (rank, share of site views).

## What to say

- Trend and rank: "#7 page, 3,100 views, −12% vs previous period".
- Sources: which channel drives it and whether that is changing.
- Engagement: avg. time, bounce, scroll depth — read together (long time + high bounce = answer page).
- Flow: where readers go next, and the biggest exit. Suggest the internal link that is missing.
- Search: the queries it ranks for and the ones slipping.
- Three specific improvements, each tied to a number above.
