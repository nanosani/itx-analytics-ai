---
name: attribution
description: Explain where the site's traffic really comes from — channel mix and trend, top referring hosts, AI-assistant referrals, campaigns — with the caveats that matter. Use when the user asks about sources, referrers, channels, UTM campaigns, or "how much traffic do we get from ChatGPT/Google/newsletter".
---

# Where traffic comes from

## Steps

1. `get_referrers` for the period: channel totals (direct, search, social, referral, AI, internal) and the top hosts.
2. Trend: `query` with `metrics: ["views","visitors"], dimensions: ["month","ref_type"]` over `period: "last_12_months"` (or `source: "monthly"` for all time).
3. Hosts: `query` with `dimensions: ["referrer"], sort: "views desc", limit: 30`; new or vanished hosts against the previous period.
4. AI assistants: `query` with `dimensions: ["referrer"], filters: {ref_type: "ai"}` — ChatGPT, Perplexity, Copilot, Claude, Gemini and the like are classified as AI. Show them by month too.
5. Campaigns: `query` with `dimensions: ["utm_source","utm_medium","utm_campaign"]` or `get_campaigns` (Pro). Offer `build_utm_link` when a source is untracked.
6. Pro: `get_breakdown` with `dimension: "ref_host"` for the one host the user cares about; `get_ecommerce` for revenue by source.

## Caveats to state

- **Direct** includes privacy browsers, native apps, email clients, and anything that strips the referrer. A rise in direct alongside a drop in social is often the same traffic.
- **Internal** is the site referring to itself (translated copies, proxies) and is excluded from source tables.
- Search engines don't send the query; only a connected search console (`get_search_queries`, Pro) shows queries.
- Channel is attributed to the session's **first** page.

## Output

Channel shares with the trend, the top 10 hosts, the AI list, campaigns, and 2–3 observations (a new host worth a link, a channel quietly growing, an untracked campaign).
