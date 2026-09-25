---
name: site-health
description: Check the technical health behind the numbers — whether hits reached the server (late/dropped hits), real-user Core Web Vitals, JavaScript errors, Google index status, and site changes. Use when traffic dips without an obvious cause, or when the user asks about speed, errors, indexing or data completeness.
---

# Speed, errors and tracking health

## Steps

1. **Is the data complete?** `get_tracking_health`. A late share above a few percent means visitors' browsers could not reach the server at first (outage, CDN 522); those hits were retried and counted, but real outages also lose visitors. Dropped hits were lost.
2. **Speed for real visitors.** `get_web_vitals` (needs the Web Vitals module on). Report p75 LCP, INP, CLS for mobile and desktop and whether each passes (good LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1). List failing pages and templates. For one page: `get_web_vitals` with `path`.
3. **Broken features.** `get_js_errors` (needs error capture on). Top errors with file and line, pages affected, anything in `spikes`. A failed script load on a tool page usually means the tool does not work at all.
4. **Indexing.** `get_index_status` when Google Search Console or Bing is connected: coverage per engine, Google's reasons for pages not indexed, pages blocked by robots.txt or noindex, pages where Google chose another canonical, fetch and crawl errors, pages known to one engine only, and Bing's crawl trend. Rank what to fix by the pages' traffic; say how many pages are still unchecked (checks run daily within API quotas). For the full rows of one problem, call it again with `list` (e.g. `not_indexed`, `blocked`, `canonical`, `errors`); for the crawl trend over months use `query` with the `crawl` family by week. Pages missing from the sitemap: `list: "not_in_sitemap"`. To get pages recrawled, `submit_for_indexing` (Bing/IndexNow; needs write access) — for Google give the user each page's `inspect_url` to click "Request indexing". Broken internal links: `get_event_rows` type `not_found`, then `get_not_found_detail` for where each 404 is linked from.
5. **What changed.** `get_annotations`: updates, theme switches, permalink or search-visibility changes, deploys.

## Output

A short list ordered by impact: what is broken or slow, the evidence, and the fix. Say plainly when a module is switched off and the question cannot be answered yet.
