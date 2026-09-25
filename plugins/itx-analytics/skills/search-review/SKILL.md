---
name: search-review
description: Review Google Search Console (or Bing) performance and turn it into ranked SEO work — striking-distance queries, titles that under-earn their position, pages competing for the same query, decliners, indexing gaps. Use when the user asks about search, SEO, Google traffic, rankings, keywords or "what should I optimise".
---

# Search console review

Needs ITX Analytics Pro with Google Search Console (or Bing Webmaster Tools) connected. Search consoles report about two days late: every tool clamps the window to the last delivered day and compares it with the period of the same length before. Quote the window the tool returns, not the one you asked for.

## Steps

1. `get_search_queries` for the period: clicks, impressions, CTR, position and their change; brand vs non-brand share; devices, countries, search appearance; the site's own CTR curve (`ctr_curve.own`) or the fallback. If `has_data` is false, say the console has not delivered data yet and stop.
2. `get_search_opportunities` — the work list:
   - `striking`: non-brand queries at positions 4–15; `potential` = extra clicks at a top-3 CTR.
   - `low_ctr`: pages on page one earning under half the CTR their position should (`missed` clicks). Title and meta description work.
   - `cannibalization`: two or more pages each taking ≥ 10% of a query's impressions.
   - `declining_pages`, `new_queries`, `rising_queries`, `falling_queries`, `zero_click`.
   - `not_ranking` (visits but no impressions) and `not_indexed`.
   - For indexing in depth (both engines, reasons, canonicals, blocks, crawl errors): `get_index_status`.
3. Evidence for the top items: `get_query_report` (which pages rank, share, trend) and `get_page_search_queries` (the page's queries with changes, CTR verdict, competing pages, index status, site visits from search). `get_search_pages` with `filter` (declining, low_ctr, not_indexed) or `sort` for page-level lists.
4. `get_annotations` for publishes, updates or deploys that line up with a change. A drop across all pages at once points at the site (indexing, speed, an update), one page at a time points at content or competition.
5. For trends beyond the tools' windows: `query` on the `search` family (by `month`, `query`, `page`) and `search_dims` (by `search_device`, `search_country`, `search_appearance`, one at a time).

## Rules

- Brand queries are excluded from opportunities; if the brand share looks wrong, tell the user to set brand terms (Settings → Search consoles).
- Bing reports weekly totals: its series has one point per week (`series_step: "week"`), and a jump in one week is a real change in Bing traffic, not a daily spike. If the overview says Google is not connected (`google_setup`), pass its message on to the user.
- Position is an impression-weighted average; lower is better. A position change under 0.5 on few impressions is noise.
- Query and page tables exclude queries Google anonymises, so they add up to less than the site totals. Do not "find" the missing clicks.
- Say when numbers are too small to act on (tens of impressions).

## Output

- Scorecard: clicks, impressions, CTR, position with changes; brand share; the data window.
- What moved and why, with numbers (queries and pages that gained or lost the most).
- The five highest-value actions, each naming the page, the query, the change (rewrite title/description, add a section answering the query, merge or interlink competing pages, fix indexing) and the clicks at stake.
