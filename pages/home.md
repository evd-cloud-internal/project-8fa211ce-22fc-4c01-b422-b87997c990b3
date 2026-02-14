---
name: Home
assetId: 3fb9a49a-df3e-4f64-9044-a46616754eee
type: page
---

# Executive KPIs

{% range_calendar id="report_dates" default_range="last 30 days" title="Reporting Period" /%}

```sql activation_rate
SELECT
    countIf(user_segment = 'Investor') * 1.0 / nullIf(count(), 0) AS value,
    (SELECT countIf(user_segment = 'Investor') * 1.0 / nullIf(count(), 0)
     FROM users_enriched
     WHERE toDate(registered_at)
         BETWEEN toDate({{report_dates.start}}) - dateDiff('day', toDate({{report_dates.start}}), toDate({{report_dates.end}})) - 1
         AND toDate({{report_dates.start}}) - 1
    ) AS prev_value
FROM users_enriched
```

```sql kyc_conversion
SELECT
    countIf(is_kyc_verified = true) * 1.0 / nullIf(count(), 0) AS value,
    (SELECT countIf(is_kyc_verified = true) * 1.0 / nullIf(count(), 0)
     FROM users_enriched
     WHERE toDate(registered_at)
         BETWEEN toDate({{report_dates.start}}) - dateDiff('day', toDate({{report_dates.start}}), toDate({{report_dates.end}})) - 1
         AND toDate({{report_dates.start}}) - 1
    ) AS prev_value
FROM users_enriched
```

```sql marketplace_revenue
SELECT
    sum(total_investment) AS value,
    (SELECT sum(total_investment)
     FROM orders_enriched
     WHERE `o.status` = 'SUCCESS' AND product_type = 'PROPERTY_SHARES'
         AND property_code NOT IN ('SAFE-21', 'SAFE-23')
         AND toDate(order_date)
             BETWEEN toDate({{report_dates.start}}) - dateDiff('day', toDate({{report_dates.start}}), toDate({{report_dates.end}})) - 1
             AND toDate({{report_dates.start}}) - 1
    ) AS prev_value
FROM orders_enriched
WHERE `o.status` = 'SUCCESS' AND product_type = 'PROPERTY_SHARES'
    AND property_code NOT IN ('SAFE-21', 'SAFE-23')
```

```sql order_success_rate
SELECT
    countIf(`o.status` = 'SUCCESS') * 1.0 / nullIf(count(), 0) AS value,
    (SELECT countIf(`o.status` = 'SUCCESS') * 1.0 / nullIf(count(), 0)
     FROM orders_enriched
     WHERE product_type = 'PROPERTY_SHARES'
         AND property_code NOT IN ('SAFE-21', 'SAFE-23')
         AND toDate(order_date)
             BETWEEN toDate({{report_dates.start}}) - dateDiff('day', toDate({{report_dates.start}}), toDate({{report_dates.end}})) - 1
             AND toDate({{report_dates.start}}) - 1
    ) AS prev_value
FROM orders_enriched
WHERE product_type = 'PROPERTY_SHARES'
    AND property_code NOT IN ('SAFE-21', 'SAFE-23')
```

```sql aum
SELECT
    sum(amount) AS value,
    (SELECT sum(amount)
     FROM contracts_enriched
     WHERE stage = 'Archived'
         AND property_code NOT IN ('SAFE-21', 'SAFE-23')
         AND toDate(`c.created_at`)
             BETWEEN toDate({{report_dates.start}}) - dateDiff('day', toDate({{report_dates.start}}), toDate({{report_dates.end}})) - 1
             AND toDate({{report_dates.start}}) - 1
    ) AS prev_value
FROM contracts_enriched
WHERE stage = 'Archived'
    AND property_code NOT IN ('SAFE-21', 'SAFE-23')
```

```sql purchases
SELECT
    count() AS value,
    (SELECT count()
     FROM orders_enriched
     WHERE `o.status` = 'SUCCESS' AND product_type = 'PROPERTY_SHARES'
         AND property_code NOT IN ('SAFE-21', 'SAFE-23')
         AND toDate(order_date)
             BETWEEN toDate({{report_dates.start}}) - dateDiff('day', toDate({{report_dates.start}}), toDate({{report_dates.end}})) - 1
             AND toDate({{report_dates.start}}) - 1
    ) AS prev_value
FROM orders_enriched
WHERE `o.status` = 'SUCCESS' AND product_type = 'PROPERTY_SHARES'
    AND property_code NOT IN ('SAFE-21', 'SAFE-23')
```

```sql new_registrations
SELECT
    count() AS value,
    (SELECT count()
     FROM users_enriched
     WHERE toDate(registered_at)
         BETWEEN toDate({{report_dates.start}}) - dateDiff('day', toDate({{report_dates.start}}), toDate({{report_dates.end}})) - 1
         AND toDate({{report_dates.start}}) - 1
    ) AS prev_value
FROM users_enriched
```

```sql new_kyc_completions
SELECT
    countIf(is_kyc_verified = true) AS value,
    (SELECT countIf(is_kyc_verified = true)
     FROM users_enriched
     WHERE toDate(registered_at)
         BETWEEN toDate({{report_dates.start}}) - dateDiff('day', toDate({{report_dates.start}}), toDate({{report_dates.end}})) - 1
         AND toDate({{report_dates.start}}) - 1
    ) AS prev_value
FROM users_enriched
```

## At a Glance

_Compared to the equivalent previous period_

{% row %}

{% big_value
    data="activation_rate"
    value="value"
    comparison={
        compare_vs="target"
        target="prev_value"
        text="vs. Previous Period"
    }
    title="Activation Rate"
    fmt="pct1"
/%}

{% big_value
    data="kyc_conversion"
    value="value"
    comparison={
        compare_vs="target"
        target="prev_value"
        text="vs. Previous Period"
    }
    title="KYC Conversion"
    fmt="pct1"
/%}

{% big_value
    data="marketplace_revenue"
    value="value"
    comparison={
        compare_vs="target"
        target="prev_value"
        text="vs. Previous Period"
    }
    title="Marketplace Revenue"
    fmt="#,##0' EGP'"
/%}

{% big_value
    data="order_success_rate"
    value="value"
    comparison={
        compare_vs="target"
        target="prev_value"
        text="vs. Previous Period"
    }
    title="Order Success Rate"
    fmt="pct1"
/%}

{% /row %}

{% row %}

{% big_value
    data="aum"
    value="value"
    comparison={
        compare_vs="target"
        target="prev_value"
        text="vs. Previous Period"
    }
    title="AUM"
    fmt="#,##0' EGP'"
/%}

{% big_value
    data="purchases"
    value="value"
    comparison={
        compare_vs="target"
        target="prev_value"
        text="vs. Previous Period"
    }
    title="Purchases"
    fmt="num0"
/%}

{% big_value
    data="new_registrations"
    value="value"
    comparison={
        compare_vs="target"
        target="prev_value"
        text="vs. Previous Period"
    }
    title="New Registrations"
    fmt="num0"
/%}

{% big_value
    data="new_kyc_completions"
    value="value"
    comparison={
        compare_vs="target"
        target="prev_value"
        text="vs. Previous Period"
    }
    title="New KYC Completions"
    fmt="num0"
/%}

{% /row %}

---

## User Acquisition & Conversion

```sql daily_users
SELECT
    toDate(registered_at) AS date,
    count() AS new_registrations,
    countIf(is_kyc_verified = true) AS kyc_completions,
    countIf(user_segment = 'Investor') AS new_investors
FROM users_enriched
GROUP BY toDate(registered_at)
ORDER BY date
```

{% combo_chart
    data="daily_users"
    x="date"
    y_fmt="num0"
    title="Daily User Activity"
%}
    {% line y="new_registrations" /%}
    {% line y="kyc_completions" /%}
    {% line y="new_investors" /%}
{% /combo_chart %}

```sql user_funnel
SELECT 'Registered' AS stage, count() AS users, 1 AS sort_order
FROM users_enriched
WHERE toDate(registered_at) BETWEEN toDate({{report_dates.start}}) AND toDate({{report_dates.end}})
UNION ALL
SELECT 'KYC Verified' AS stage, countIf(is_kyc_verified = true) AS users, 2 AS sort_order
FROM users_enriched
WHERE toDate(registered_at) BETWEEN toDate({{report_dates.start}}) AND toDate({{report_dates.end}})
UNION ALL
SELECT 'Investor' AS stage, countIf(user_segment = 'Investor') AS users, 3 AS sort_order
FROM users_enriched
ORDER BY sort_order
```

{% funnel_chart
    data="user_funnel"
    category="stage"
    value="users"
    title="User Conversion Funnel"
    value_fmt="num0"
    order="sort_order"
/%}

---

## Revenue & Transactions

```sql daily_revenue
SELECT
    toDate(order_date) AS date,
    sum(total_investment) AS revenue,
    count() AS order_count
FROM orders_enriched
WHERE `o.status` = 'SUCCESS'
    AND product_type = 'PROPERTY_SHARES'
    AND property_code NOT IN ('SAFE-21', 'SAFE-23')
GROUP BY toDate(order_date)
ORDER BY date
```

{% area_chart
    data="daily_revenue"
    x="date"
    y="revenue"
    title="Daily Marketplace Revenue"
    y_fmt="#,##0' EGP'"
/%}

{% bar_chart
    data="daily_revenue"
    x="date"
    y="order_count"
    title="Daily Purchase Volume"
    y_fmt="num0"
/%}

```sql cumulative_revenue
SELECT
    toDate(order_date) AS date,
    sum(sum(total_investment)) OVER (ORDER BY toDate(order_date)) AS cumulative_revenue
FROM orders_enriched
WHERE `o.status` = 'SUCCESS'
    AND product_type = 'PROPERTY_SHARES'
    AND property_code NOT IN ('SAFE-21', 'SAFE-23')
GROUP BY toDate(order_date)
ORDER BY date
```

```sql orders_by_status
SELECT
    `o.status` AS status,
    count() AS order_count
FROM orders_enriched
WHERE product_type = 'PROPERTY_SHARES'
    AND property_code NOT IN ('SAFE-21', 'SAFE-23')
GROUP BY `o.status`
ORDER BY order_count DESC
```

{% row %}

{% area_chart
    data="cumulative_revenue"
    x="date"
    y="cumulative_revenue"
    title="Cumulative Revenue"
    y_fmt="#,##0' EGP'"
/%}

{% bar_chart
    data="orders_by_status"
    x="status"
    y="order_count"
    title="Orders by Status"
    y_fmt="num0"
/%}

{% /row %}

---

## Conversion Rate Trends

```sql weekly_rates
SELECT
    toStartOfWeek(toDate(registered_at)) AS week,
    countIf(user_segment = 'Investor') * 1.0 / nullIf(count(), 0) AS activation_rate,
    countIf(is_kyc_verified = true) * 1.0 / nullIf(count(), 0) AS kyc_rate
FROM users_enriched
GROUP BY toStartOfWeek(toDate(registered_at))
ORDER BY week
```

{% combo_chart
    data="weekly_rates"
    x="week"
    y_fmt="pct1"
    title="Weekly Activation & KYC Rates"
%}
    {% line y="activation_rate" /%}
    {% line y="kyc_rate" /%}
{% /combo_chart %}
