---
name: analytics-query
description: Write correct calls to the ITX Analytics `query` tool — metrics, dimensions, filters, families, sorting, paging — for questions the fixed reports do not answer. Use when a question needs a custom breakdown (e.g. mobile views by week for one page, countries that grew most, AI referrals by month).
---

# The `query` tool

`query` builds a prepared aggregate SQL statement from named metrics, dimensions and filters. It never returns visitor rows.

Read `get_schema` once; it lists every family, dimension, metric, filter and value name.

## Shape

```json
{
  "metrics": ["views", "visitors"],
  "dimensions": ["week", "device"],
  "filters": {"page": "/pricing/"},
  "period": "last_90_days",
  "sort": "week asc",
  "limit": 100
}
```

## Families (the dimensions pick the table)

| Family | Dimensions | Metrics | Notes |
|---|---|---|---|
| daily (default) | date, week, month, day_of_week, page, post_type, ref_type, country, device, campaign, utm_source, utm_medium, utm_campaign | all | ≤ 400 days |
| monthly | month, page, post_type, ref_type, country, device | all | all-time; auto for long/old ranges, or `source: "monthly"` |
| hourly | hour, date, page, post_type | views, visitors, sessions | last ~35 days |
| refs | date/week/month/day_of_week, referrer, referrer_url, ref_type | views, visitors | internal excluded unless filtered |
| tech | date/week/month/day_of_week, device, browser, os | views, visitors | |
| lang | date/week/month/day_of_week, language | views, visitors | |

Dimensions from different families cannot be mixed (referrer × browser, page × browser). Split into two queries instead.

## Metrics

views, visitors, sessions, bounces, bounce_rate, exits, exit_rate, engaged_seconds, avg_time, views_per_session.

## Filters

Equality, string or array: page (path, `*` wildcard), post_type, ref_type, country, device, campaign_id, utm_source, utm_medium, utm_campaign, referrer (host), browser, os, language. Names work for enums: `ref_type: "search"`, `device: "mobile"`, `browser: "safari"`.

## Rules that affect correctness

- With no page/bundle dimension and no filter, the query reads **site-total rows** (exact visitors). With any dimension or filter it reads per-page bundle rows, so visitors are summed per bundle and over-count people who span pages/countries/devices. Say which one you used when the number matters.
- `sessions` on per-page rows = entries (sessions that started there).
- Rows are capped at 200; `truncated: true` means use `offset` or a tighter filter.
- Default sort: first metric desc, or the time dimension asc when only time dimensions are used.
- Dates are UTC days.

## Patterns

- Trend of a segment: `dimensions: ["date"], filters: {ref_type: "ai"}`.
- Growth ranking: run the same query for two periods and diff in your head/table.
- Weekday profile: `dimensions: ["day_of_week"]`, `metrics: ["views","visitors"]`, `period: "last_90_days"`.
- Best hours: `dimensions: ["hour"]`, `period: "last_7_days"`.
- Campaign performance: `dimensions: ["utm_source","utm_medium","utm_campaign"], metrics: ["visitors","sessions","bounce_rate"]`.
