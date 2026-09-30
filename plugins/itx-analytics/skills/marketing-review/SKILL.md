---
name: marketing-review
description: The marketing morning check and campaign questions — which channels, campaigns and pages bring visitors, sign-ups and paying customers, first- or last-touch, with the funnel. Use when the user asks about conversions, sign-ups, trials, upgrades, revenue by source, campaign or content ROI, the funnel, or "what happened yesterday".
---

# Marketing review

Needs ITX Analytics Pro 0.37+ with conversions sent from the site (`do_action( 'itxa_track', … )`). `get_site_info` → `integrations.marketing` lists the conversion events seen.

## Morning check

1. `get_daily_digest` (yesterday vs the same weekday last week): traffic, conversions, paid, revenue, top channels/referrers/campaigns, top pages, search clicks, annotations.
2. Anything that moved more than ~30 %: `get_acquisition` with `dimension: "channel"` (then `platform`, `referrer` or `landing_page`) for that day and the week before.
3. `get_annotations` before blaming a channel — a deploy, a post or an ad going live explains most jumps.

## Campaigns and channels

- `get_marketing_overview` with `compare: "previous_period"` for the headline.
- `get_acquisition` by `channel`, then `utm_campaign` / `utm_content` for tagged traffic; `model: "first"` (what brought people in) vs `model: "last"` (what brought them back before converting). Report both when they disagree.
- Event rates (`<event>_rate`) are conversions per visit of that group — compare channels on rates, not raw counts.
- `get_funnel` with `by: "channel"` or `by: "week"` to find the leaking step.

## Content

- `get_content_performance` with `prefix: "/blog/"` (or one `url`): entrances by channel, scroll depth, CTA clicks, sign-ups that landed there first and sign-ups whose last page was there.

## Caveats to state

- Dates are UTC days.
- Visits before `coverage.visits_per_visit_since` come from daily totals: no referrer host or platform for organic traffic, engaged visits approximated.
- "unattributed" conversions arrived without any touch (server-to-server events, visitors who blocked storage or sent Do Not Track).
- The funnel counts people per stage; visits are not linked to accounts, so it is not a per-person path.
- Channels follow the site's editable rules (tags beat the referrer); a rule change recolours history.

## Output

Three to five lines for the meeting: what changed, why (with the evidence), and one action (a campaign to scale, a page to fix, a step in the funnel to test).
