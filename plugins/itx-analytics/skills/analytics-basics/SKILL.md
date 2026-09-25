---
name: analytics-basics
description: How to answer any question about the site's traffic with the ITX Analytics MCP tools — which tool to call, how metrics are defined, and the caveats to state. Use whenever the user asks about visitors, views, pages, sources, countries, devices, real time or trends.
---

# Answering analytics questions with ITX Analytics

The `itx-analytics` MCP server exposes the site's own analytics (self-hosted, cookieless, aggregated — no visitor-level rows).

## First call of a conversation

Call `get_site_info` once. It returns the site name, time zone, edition (free/pro), the first tracked month, retention windows, the metric glossary and the list of tools. Do not guess at any of these.

## Which tool

| Question | Tool |
|---|---|
| Headline numbers, trend, compare with last period/year | `get_overview` (`compare: "period"` or `"year"`) |
| Best/worst pages | `get_pages` (`orderby`, `limit`, `page`) |
| Where traffic comes from | `get_referrers` (channel totals + hosts) |
| Countries / devices / browsers | `get_countries`, `get_tech` |
| Scroll depth, outbound clicks, downloads, forms, 404s, custom events | `get_events`, then `get_event_rows` with a `type` |
| Who is on the site right now, is today normal | `get_realtime` |
| Anything custom (metric × dimension × filter) | `query` — read `get_schema` first |
| One page in depth (Pro) | `get_page_report` |
| One referrer / country / device / campaign in depth (Pro) | `get_breakdown` |
| What visitors do, path by path (Pro, last 48 h) | `get_journeys`, `get_flows` |
| Campaigns, content performance, store, goals, search queries (Pro) | `get_campaigns`, `get_content`, `get_ecommerce`, `get_goals`, `get_search_queries` |
| Did visitors get what they came for (reference/tool sites) (Pro) | `get_outcomes` — outcome rate and served bounces, not bounce rate |
| What visitors searched, typed or chose inside an event (Pro) | `get_event_properties` (`event`, optional `prop`) |
| Where visitors go next, dead ends, for any range (Pro) | `get_page_flow` (with or without `path`) |
| Real-user speed: LCP, INP, CLS (Pro, when switched on) | `get_web_vitals` |
| Front-end errors visitors hit (Pro, when switched on) | `get_js_errors` |
| Is the data complete (late/dropped hits) | `get_tracking_health` |
| Is every page indexed on Google and Bing, and why not (Pro) | `get_index_status` (whole site, one `path`, or filtered by status) |
| Search performance with changes, brand split, devices, countries (Pro) | `get_search_queries` |
| SEO work ranked by clicks at stake (Pro) | `get_search_opportunities`, then `get_query_report` / `get_page_search_queries` |
| Every page's search metrics next to its traffic (Pro) | `get_search_pages` |
| Site changes that explain a trend (publishes, updates, deploys) | `get_annotations` (also returned by `get_overview` and page reports) |
| Build a tracked link | `build_utm_link` |

Prefer the fixed report tools for the dashboard's own tables (they match what the user sees) and `query` for everything else.

## Date ranges

Every report tool accepts `period` (`today`, `yesterday`, `last_7_days`, `last_28_days`, `last_30_days`, `last_90_days`, `this_month`, `last_month`, `this_year`, `last_12_months`) or explicit `from`/`to` (YYYY-MM-DD). **All dates are UTC days**, which is also what the dashboard shows. Default when unspecified: last 28 days.

## Metric definitions (say them when it matters)

- **views** — page loads (reloads count).
- **visitors** — unique per UTC day. Summing visitors across days or across pages over-counts returning people; when you add them up, say so, or use the site-level number from `get_overview`.
- **sessions** — visits; a session ends after 30 minutes of inactivity. On per-page rows, sessions means *entries* (sessions that started on that page).
- **bounce rate** — single-page sessions ÷ sessions.
- **avg. time** — engaged seconds ÷ views (tab visible and the visitor active). Long time + high bounce = a good answer page, not a problem.
- **ref_type** — channel of the session's first page: direct, search, social, referral, internal, AI. Direct includes privacy browsers and apps that strip referrers.

## Before explaining a change

1. `get_annotations` for the range — a plugin update, a publish, a permalink change or a deploy often explains it.
2. `get_tracking_health` — if many hits arrived late or were dropped, the server was unreachable and the "drop" may be an artefact.

## Reference and tool sites

A visitor who copies a value and leaves in eight seconds was served. When the owner has defined outcomes, use `get_outcomes`: *outcome rate* and *served bounces* replace bounce rate and time on page as the success measures. Never call a served bounce a failure.

## Writing the answer

- Lead with the number and the comparison ("12,400 views, up 8% on the previous 28 days").
- Give the 2–3 drivers with figures, not a wall of tables.
- Name the caveat that applies (visitor sums, short ranges, UTC days, raw window for real time/journeys).
- Suggest a concrete next step only when the data supports it.
