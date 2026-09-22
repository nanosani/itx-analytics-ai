---
name: traffic-change
description: Diagnose why traffic went up or down — rank the pages, channels, countries and devices that explain the difference against a comparison period, using the ITX Analytics query tool. Use when the user asks "why did traffic drop/spike", "what changed", or notices an anomaly.
---

# What changed and why

Traffic changes are almost always concentrated: a few pages, one channel, one country. Find the concentration, then explain it.

## Steps

1. Pin the periods. Current = what the user is worried about; comparison = the previous period of the same length (or the same weekdays). `get_overview` with `compare: "period"` gives both totals.
2. Contribution analysis with `query`, running each breakdown for **both** periods and ranking by absolute change:
   - `dimensions: ["ref_type"]`
   - `dimensions: ["page"], sort: "views desc", limit: 50`
   - `dimensions: ["country"], limit: 20`
   - `dimensions: ["device"]`
   - `dimensions: ["referrer"]` (marginal table; views/visitors only)
3. Zoom in on the top contributor:
   - a page: `get_page_report` (Pro) or `query` with `filters: {page: "/path/"}` by `date` and `ref_type`;
   - a host: `get_breakdown` with `dimension: "ref_host"`;
   - a day: `query` with `dimensions: ["date"]` and the relevant filter to see whether it is a step change or a spike.
4. Check the usual suspects: `get_event_rows` `type: "not_found"` (broken URLs), `get_annotations` (deploys, campaigns), `get_realtime` (is it still happening), `get_search_queries` (Pro; lost rankings).

## Output

- **Top 5 contributors** to the change: item, before → after, share of the total change.
- **Most likely explanation** in one paragraph, with the evidence.
- **What to verify** (things the data cannot show: a ranking change, a broken redirect, a paused campaign).

Be honest about uncertainty. A drop spread evenly across everything usually means measurement (tracking, caching, bots) rather than audience; say so and suggest checking Site Health.
