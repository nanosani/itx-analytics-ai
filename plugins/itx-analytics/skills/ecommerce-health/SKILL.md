---
name: ecommerce-health
description: Assess store performance from ITX Analytics Pro — orders, net revenue, conversion, AOV, sources and landing pages that sell, product funnel, checkout funnel and cart abandonment, cohorts. Use when the user asks about sales, revenue, conversion, WooCommerce/EDD/SureCart/FluentCart/PMPro results.
---

# E-commerce health check (Pro)

`get_ecommerce` returns everything for a range; call it for the period and, separately, for the previous period to compare.

## Read in this order

1. **Totals**: orders, gross → net revenue (refunds), conversion rate (orders ÷ sessions), AOV, earnings per visitor, new vs returning customers.
2. **Sources that sell**: `by_ref`, `by_landing`, `by_campaign` — each with sessions and conversion rate. Call out channels with lots of sessions but a low rate, and small channels with a high rate.
3. **Product funnel**: views → carts → orders per product with the two rates; products with views but no carts are pricing/page problems, carts but no orders are checkout problems.
4. **Checkout funnel + abandonment** (WooCommerce): product visitors → cart → checkout → orders; abandonment by device and referrer type over the raw window.
5. **Content that sells**: pages seen in buying sessions with assisted revenue and revenue per 1,000 views — the blog-ROI answer.
6. **Cohorts**: customers by first source with repeat rate and 90-day value.
7. Coupons, payment methods, search-to-sales when present.

## Output

- Scorecard vs previous period (orders, net revenue, conversion, AOV, EPV).
- The best and worst converting sources with numbers.
- The product funnel's two biggest leaks.
- Three highest-impact fixes, each tied to a metric.

Mention that conversion uses site sessions as the denominator and revenue is net of refunds.
