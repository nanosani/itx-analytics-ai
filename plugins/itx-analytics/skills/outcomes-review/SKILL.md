---
name: outcomes-review
description: Judge whether visitors got what they came for — outcome rate, served bounces, zero-result searches, tool failures and dead ends — instead of bounce rate and time on page. Use for reference, lookup and tool sites, or when the user asks whether pages "work" or "help".
---

# Did visitors get what they came for?

Bounce rate and time on page mislead on sites where the answer takes seconds. ITX Analytics lets the owner mark **outcome events** (Settings → Outcomes: custom events like `copy:*` or `tool:use:*`, plus download, form, outbound, purchase).

## Steps

1. `get_outcomes` for the period and the previous one.
   - `outcome_rate` = sessions with an outcome ÷ sessions.
   - `served_bounces` = single-page visits that still reached an outcome. `unserved_bounce_rate` is the bounce rate that matters.
   - If `configured` is false, list the site's custom events (`get_event_rows` with `type: "custom"`) and recommend which to mark as outcomes. Stop there.
2. Landing pages: rank by sessions, flag those with a low outcome rate *and* a high unserved bounce rate.
3. What visitors asked for: `get_event_properties` for search and tool events — zero-result searches (`results` = 0 or `search:no-results`), finder inputs with no match, tool error reasons. These are content and product gaps.
4. `get_page_flow` without a path: dead ends that are not also served pages.
5. `get_annotations` for changes that line up with a shift.

## Output

- Scorecard: outcome rate, served share of bounces, vs the previous period.
- Pages that serve visitors well (keep, link to them).
- Pages that fail them, each with the evidence (outcome rate, unserved bounces, dead end, zero-result searches that land there).
- The top 10 things visitors looked for and did not find.
- Three fixes, most valuable first.
