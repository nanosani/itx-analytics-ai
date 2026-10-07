# Tool reference

All tools are read-only unless marked *write*. Every date argument is a UTC day. Report tools take `from`/`to` (YYYY-MM-DD) or `period` (`today`, `yesterday`, `last_7_days`, `last_28_days`, `last_30_days`, `last_90_days`, `this_month`, `last_month`, `this_year`, `last_12_months`); default is the last 28 days. Row limits are capped at 200.


## Result shape (0.39+)

Every tool returns:

- `meta` — `from`/`to` (UTC days), `filters`, `source`, `data_until`, `timezone`, `aggregated_at`, `people_only` (automated traffic excluded), `definitions` for the metrics used, `lists` (the list-valued keys), `rows_from` (the key the main list had before 0.39), `empty` (why `rows` is empty), and `coverage` (0.40.5) for tools that read data with a start date: `page_flow`, `web_vitals`, `google_search_console` (with `history_import` {done, total, running} while the 16-month import runs) and `bing_webmaster_tools`, each with `since`, a `note`, and `partial` when the requested range starts before `since`. A short history there means new, not broken.
- `totals` where the tool has them.
- `rows` — the main list.

Invalid arguments return an error naming the fix (unknown argument, missing required value, value outside an enum, bad date) instead of an empty result. Visitors are not additive across rows: use `totals`.

## Free

| Tool | Arguments | Returns |
|---|---|---|
| `get_site_info` | — | Site name/URL/time zone, edition, first tracked month, retention, glossary, tool list. Call first. |
| `get_overview` | dates, `compare` (none/period/year), `granularity`, `filters` | Totals (views, visitors, sessions, bounces, engaged, bounce_rate) + series, optional comparison block. |
| `get_pages` | dates, `limit`, `page`, `orderby`, `order`, `filters` | Pages with views, visitors, entries, bounce rate, avg. time, exits. |
| `get_referrers` | dates, `limit`, `page`, `orderby`, `filters` | Channel totals and top hosts. |
| `get_countries` | dates, `limit`, `filters` | Views/visitors by country. |
| `get_tech` | dates | Devices, browsers, operating systems. |
| `get_events` | dates | Scroll milestones, outbound clicks, custom events. |
| `get_event_rows` | dates, `type` (clicks/downloads/forms/not_found/custom/scroll), `limit`, `page` | One event type as a table. |
| `get_realtime` | — | Online now, baselines (yesterday/last week/last month at this time), today so far, hourly overlay. Pro adds pages/referrers/countries now, the live feed, trending, new sources, unusual activity, sales. |
| `query` | `metrics`, `dimensions`, `filters`, dates, `sort`, `limit`, `offset`, `source` | Custom aggregate: rows, totals, scope note, truncated flag. See the `analytics-query` skill. |
| `get_schema` | — | Families, dimensions, metrics, filters, value names, rules, examples, glossary. |
| `get_annotations` | dates | Site changes on the timeline: publishes, WordPress/plugin/theme updates, setting changes, deploys, notes (with `kind`). |
| `add_annotation` *(write)* | `label`, `date`, `color` | Adds a timeline marker. OAuth connections need `analytics:write`. |
| `get_tracking_health` | `days` | Late, retried, dropped and duplicate hits per day with a verdict (Pro adds JS errors). |
| `get_site_brief` | dates or `period` (default last 7 days), `compare` | One-call status report: people-only traffic with changes, automated traffic filtered, channels, top pages and referrers, unusual days with explanations, tracking health, annotations; Pro adds conversions, search totals and opportunities. |
| `get_automated_traffic` | dates | What the automated-traffic filter removed: views, visitors, share, series, reasons, countries, pages, referrers. |
| `get_visit_log` | `automated` (all/yes/no), `limit`, `offset` | Last ~48 h of visitor-days with automation score and signals. |
| `build_utm_link` | `url`, `source`, `medium`, `campaign`, `term`, `content` | A tracked link. |

Filters (`filters` object): `country`, `device` (desktop/mobile/tablet), `ref_type` (direct/search/social/referral/internal/ai), `dow` (1 = Sunday … 7). Pro adds `path`, `post_type`, `author`, `category`, `campaign`, `utm_source`, `utm_medium`.

## Pro

| Tool | Arguments | Returns |
|---|---|---|
| `get_page_report` | `path`, dates, `compare` | Totals, trend, breakdowns, scroll depth, insight, in-site previous/next pages, inbound links. |
| `get_breakdown` | `dimension` (ref_host/ref_type/country/region/device/browser/os/campaign), `value`, dates, `compare` | Drill-down for one value. |
| `get_journeys` | `limit` (≤ 50), `segment`, `page`, `host`, `country` | Recent sessions as page sequences (raw window). |
| `get_flows` | `path` (optional focus) | Entry pages, common paths, exits, single-page share. |
| `get_campaigns` | dates | UTM campaigns. |
| `get_content` | dates | Content performance by post, author, category, word count, decay/evergreen. |
| `get_ecommerce` | dates | Store report: totals, by source/landing/campaign/device/country, product funnel, checkout funnel, abandonment, content that sells, cohorts, coupons, payments. |
| `get_goals` | dates | Goals with completions; funnels with drop-off. |
| `get_search_queries` | dates, `source` (auto/google/bing); optional `search`, `filter` (branded/unbranded/questions/new/lost/striking/top3/page1), `sort`, `order`, `limit`, `page` | Search overview vs the previous period of equal length: clicks, impressions, CTR, position with changes, daily (or weekly) series, devices, countries, search appearance, brand vs non-brand, question queries, top queries and pages, the CTR curve, index summary. With a filter or search it also lists matching queries. |
| `get_search_pages` | dates, `source`, `search`, `filter` (declining/improving/low_ctr/not_indexed), `sort`, `order`, `limit`, `page` | Every page with search metrics and changes next to its site views and search-referred views, query count, CTR vs expected for its position, index status. |
| `get_search_opportunities` | dates, `source`; optional `min_impressions`, `position_min`, `position_max`, `include_branded`, `limit` (recompute striking distance with your thresholds) | Ranked SEO work: striking-distance queries with potential clicks, low CTR for the position, pages competing for a query, declining pages, new/rising/falling/zero-click queries, visited-but-invisible content, pages not indexed. |
| `get_query_report` | `query`, dates, `source` | One query: totals and changes, series, the pages ranking for it with share of impressions (cannibalization flag), brand/question labels. |
| `get_page_search_queries` | `path`, dates, `source` | One page's search picture: totals and changes, CTR verdict for its position, series, every query with changes, striking-distance queries, competing pages, index status, site visits from search, Bing queries. |
| `get_index_status` | none; or `path`; or `list` (not_indexed, new_waiting, not_in_sitemap, blocked, canonical, errors, google_only, bing_only, unchecked); or filters `google`, `bing`, `type`, `flag` (canonical/blocked/fetch/no_sitemap), `search`, `sort`, `order`; `limit`, `page`, `crawl_days` | Indexing on Google and Bing for every published page (builder templates and SEO-plugin noindex pages excluded and counted). Summary: coverage per engine, Google's reasons, full counts and top rows of every problem list, Bing crawl trend and crawl issues. `path`: one page. `list`: every row of one problem list, paginated. Filters: the page table. |
| `get_not_found_detail` | `path`, dates | One 404: first/last seen, daily hits, where visitors came from (internal pages with the broken link, external sites), redirect suggestions. |
| `get_outbound_detail` | `host`, dates | Clicks to one external site: daily series, target URLs, the pages the clicks came from. |
| `submit_for_indexing` | `paths` or `bulk: "bing_missing"`, `engine` (auto/bing/indexnow) | Write: ask Bing (Webmaster API within its daily quota, or IndexNow) to crawl pages. Google has no API — rows from `get_index_status` carry `inspect_url`: the page's Search Console inspection (with "Request indexing") when `inspect_exact` is true, otherwise the property's Search Console home where `page_url` is pasted into the inspect bar. |
| `check_index_now` | `pages` (1–20) | Write: run Google URL Inspection and Bing URL info checks now, within the daily limits. |
| `get_event_properties` | `event`, `prop`, dates, `limit` | Custom-event properties for any range: top values, shares, numeric summary, daily series, pages. |
| `get_outcomes` | dates, `limit` | Outcome rate, served bounces, landing pages. |
| `get_page_flow` | `path`, `direction`, dates, `limit` | Came from / went to next for a page, or dead ends and busiest steps. |
| `get_web_vitals` | `path`, `metric`, `device`, dates | Real-user p75 LCP/INP/CLS/FCP/TTFB, pass/fail, failing pages and templates. |
| `get_js_errors` | `path`, dates, `limit`, `include_blocked` | The site's own JavaScript errors (blocked trackers/ads and browser noise counted in `by_origin`, shown with `include_blocked`), pages affected, spikes. |
| `get_insights` | — | A blog-style year in review (not an analysis — use `get_site_brief`). |
| `get_marketing_overview` | dates, `compare` (previous_period/previous_year/none), `filters`, `model` (first/last) | Visitors, page views, visits, engaged visits, every conversion event, paying customers and revenue, with % change. |
| `get_acquisition` | `dimension` (channel, platform, utm_source, utm_medium, utm_campaign, utm_content, utm_term, landing_page, referrer, country, device, date, week), dates, `metrics[]`, `filters{}`, `compare`, `model`, `limit`, `sort` | Rows per dimension value: visits, engaged visits, engagement rate, page views, bounces, avg engaged seconds, conversions per event and per visit, paid, revenue; totals; coverage note for days before per-visit data. |
| `get_timeseries` | `metric` (visits, engaged_visits, pageviews, bounces, paid, revenue, conversions, or an event name), dates, `interval` (day/week), `filters`, `model` | Zero-filled series and total. |
| `get_funnel` | `steps[]` (default landing + configured/seen events), dates, `by` (week, channel, campaign, any acquisition dimension), `filters`, `model` | Stage counts with step and overall rates, optionally per group. Stage counts, not a per-person path. |
| `get_content_performance` | `url` or `prefix`, dates | Page views, visitors, entrances by channel, bounce rate, avg engaged time, scroll-depth distribution, CTA clicks, conversions that landed there first and whose last page was there, top pages in a section. |
| `get_conversions` | dates; optional `event`, `by` (a property or an acquisition dimension), `model`, `limit` | Server-side conversion events (`itxa_track`): counts, distinct accounts, revenue, property names, attribution source; or one event broken down. |
| `get_search_performance` | dates, `engine` (google/bing/both/auto), `dimension` (query/page/query_page), `query_contains`, `page_contains`, `position_min`, `position_max`, `min_impressions`, `sort`, `order`, `limit`, `compare` | Clicks, impressions, CTR, position rows; both engines summed with a per-engine split. |
| `get_new_search_queries` | `engine`, `days` (default 7), `limit` | Queries first seen in the stored history within the last N days of delivered data; `reliable` flags short histories. |
| `get_daily_digest` | `date` (default yesterday) | That day vs the same weekday a week earlier: traffic, conversions, paid, revenue with changes; top channels, referrers, campaigns (with conversions, last touch); top pages; Google/Bing clicks for their latest day; annotations. |
| `get_search_phrasing` | dates, `engine` | Impressions/CTR/position per query modifier (units, questions, vs, intent, years, own) and recurring phrases. |
| `get_title_suggestions` | dates, `path` (optional), `engine` | Words searchers use that a page's title lacks; without path, the busiest pages with gaps. |
| `get_search_url_issues` | dates, `check_now` (0–20) | Search URLs that are not the canonical page: redirects (merged in reports), errors, canonical elsewhere, noindex, not a published page. |
| `get_change_impact` | `date` or `annotation_id`, `paths[]` (`/blog/*`), `days` (≤ 90), `engine` | Before/after a change against the rest of the site: traffic and search metrics with expected values and effect, per-page rows, queries that moved. |
| `get_watchlist` | — | Watched pages/queries: last 7 days vs the 7 before, position move, alerts. |
| `watch` *(write)* | `action` (add/remove), `type`, `value`, `threshold`, `id` | Edit the watchlist. |
| `create_goal` *(write)* | `name`, `type` (event/url), `match` | Creates a goal. Needs `analytics:write`. |

## Resources

- `itx-analytics://glossary` — metric definitions (Markdown).
- `itx-analytics://schema` — the query schema (JSON).

## Prompts

`weekly_review`, `what_changed`, `content_to_refresh`, `attribution_check`, `page_review` (`path`), `outcomes_review`, `site_health_check`, `search_review`, `ecommerce_health` — each takes an optional `period`.

## Protocol notes

- Every tool result carries `meta` (see Result shape above).

- Two URLs: `…/mcp` (401 advertises OAuth discovery) and `…/mcp/key` (401 challenges Basic only, for key-based tools). Same server, same auth rules.
- Transport: Streamable HTTP, stateless (no `Mcp-Session-Id`, GET returns 405, DELETE 204). Protocol versions 2025-06-18, 2025-03-26, 2024-11-05; JSON-RPC batches accepted.
- Auth: `Authorization: Basic <application password>` or `Authorization: Bearer <OAuth token>`. A 401 carries `WWW-Authenticate: Bearer … resource_metadata="…"` for OAuth discovery.
- OAuth 2.1: `/.well-known/oauth-protected-resource`, `/.well-known/oauth-authorization-server`, dynamic client registration, authorization code + PKCE (S256), refresh-token rotation, revocation. Scopes `analytics:read`, `analytics:write`.
- Browser clients: the `Origin` header must be the site's host or localhost (DNS-rebinding guard).
