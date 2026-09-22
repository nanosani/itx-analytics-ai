# Tool reference

All tools are read-only unless marked *write*. Every date argument is a UTC day. Report tools take `from`/`to` (YYYY-MM-DD) or `period` (`today`, `yesterday`, `last_7_days`, `last_28_days`, `last_30_days`, `last_90_days`, `this_month`, `last_month`, `this_year`, `last_12_months`); default is the last 28 days. Row limits are capped at 200.

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
| `get_search_queries` | dates | Search-console queries and pages (when connected). |
| `get_page_search_queries` | `path` | Queries for one page. |
| `get_insights` | — | Year in review, all-time totals, popular time, latest/top posts, posting activity, heatmap. |
| `get_annotations` | dates | Timeline annotations. |
| `add_annotation` *(write)* | `date`, `label`, `color` | Adds a timeline marker. Needs `analytics:write`. |
| `create_goal` *(write)* | `name`, `type` (event/url), `match` | Creates a goal. Needs `analytics:write`. |

## Resources

- `itx-analytics://glossary` — metric definitions (Markdown).
- `itx-analytics://schema` — the query schema (JSON).

## Prompts

`weekly_review`, `what_changed`, `content_to_refresh`, `attribution_check`, `page_review` (`path`), `ecommerce_health` — each takes an optional `period`.

## Protocol notes

- Transport: Streamable HTTP, stateless (no `Mcp-Session-Id`, GET returns 405, DELETE 204). Protocol versions 2025-06-18, 2025-03-26, 2024-11-05; JSON-RPC batches accepted.
- Auth: `Authorization: Basic <application password>` or `Authorization: Bearer <OAuth token>`. A 401 carries `WWW-Authenticate: Bearer … resource_metadata="…"` for OAuth discovery.
- OAuth 2.1: `/.well-known/oauth-protected-resource`, `/.well-known/oauth-authorization-server`, dynamic client registration, authorization code + PKCE (S256), refresh-token rotation, revocation. Scopes `analytics:read`, `analytics:write`.
- Browser clients: the `Origin` header must be the site's host or localhost (DNS-rebinding guard).
