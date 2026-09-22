---
name: weekly-review
description: Produce a concise weekly (or any period) traffic review from ITX Analytics — totals vs the previous period, what moved and why, pages and sources worth attention, actions. Use when the user asks for a report, review, summary or "how did the site do".
---

# Weekly review

Goal: a report the site owner can read in two minutes and act on.

## Steps

1. `get_site_info` (once per conversation).
2. `get_overview` for the period with `compare: "period"`. Note views, visitors, sessions, bounce rate, avg. time and their % change.
3. `get_pages` (`limit: 15`), `get_referrers`, `get_countries` (`limit: 10`), `get_tech` for the same period.
4. Explain the biggest change with `query`:
   - by channel: `metrics: ["views","visitors"], dimensions: ["ref_type"]` for this period and the previous one;
   - by page: `dimensions: ["page"], sort: "views desc", limit: 25` for both periods, then diff;
   - by week if the period is long: `dimensions: ["week","ref_type"]`.
5. `get_event_rows` with `type: "not_found"` to catch broken pages; `get_annotations` (Pro) for known events in the range.
6. Pro: `get_realtime` for today's pace, `get_ecommerce` if a store is connected, `get_search_queries` if a search console is connected.

## Output format

```
## <Site> — <period>

**Headline:** 12,400 views (+8%), 7,900 visitors (+5%), 41% bounce (−2 pts), 1:12 avg. time.

**What moved**
1. Search traffic +18% — /guide-x/ and /guide-y/ picked up ~900 views after …
2. …
3. …

**Worth attention**
- /old-post/ lost 40% of its search views for the second period in a row.
- 3 new 404 paths with >20 hits: …

**Actions**
1. …
2. …
3. …

_Caveats: dates are UTC days; visitor totals are per-day uniques._
```

Keep it to the numbers that changed. Skip sections with nothing to say.
