# Marketing SQL Analysis Reference Guide

This repository serves as a personal reference guide for analyzing digital marketing campaigns using SQL and business intelligence tools.

## 1. Sample Marketing Dataset (Copy & Paste to Practice)
You can use this SQL script to create a practice table and load mock campaign data into any SQL environment (like MySQL or PostgreSQL).

```sql
-- Create the practice table framework
CREATE TABLE marketing_campaigns (
    campaign_id INT PRIMARY KEY,
    campaign_date DATE,
    channel VARCHAR(50),
    impressions INT,
    clicks INT,
    conversions INT,
    ad_spend DECIMAL(10,2),
    revenue DECIMAL(10,2)
);

-- Insert mock data for April 2026
INSERT INTO marketing_campaigns VALUES
(1, '2026-04-05', 'Google', 150000, 4500, 180, 12000.00, 39600.00),
(2, '2026-04-12', 'Facebook', 200000, 8000, 220, 15000.00, 12000.00),
(3, '2026-04-18', 'TikTok', 310000, 12500, 310, 8500.00, 9350.00),
(4, '2026-04-22', 'Google', 95000, 2800, 110, 7500.00, 21000.00),
(5, '2026-04-29', 'Facebook', 180000, 6100, 140, 11000.00, 8500.00);
```

## 2. The Performance Analysis Query
This query cleans the data, calculates critical marketing KPIs, and flags underperforming campaigns using logical thresholds.

```sql
SELECT 
    channel,
    SUM(ad_spend) AS total_spend,
    SUM(revenue) AS total_revenue,
    
    -- Return on Ad Spend (ROAS)
    ROUND(SUM(revenue) / NULLIF(SUM(ad_spend), 0), 2) AS roas,
    
    -- Click-Through Rate (CTR) Percentage
    ROUND((SUM(clicks) / NULLIF(SUM(impressions), 0)) * 100, 2) AS click_through_rate_pct,
    
    -- Customer Acquisition Cost (CAC)
    ROUND(SUM(ad_spend) / NULLIF(SUM(conversions), 0), 2) AS customer_acquisition_cost,
    
    -- Automated Insight Logic
    CASE 
        WHEN SUM(revenue) / NULLIF(SUM(ad_spend), 0) < 1.0 THEN '⚠️ LOSSMAKER: Pause Spend'
        WHEN SUM(ad_spend) / NULLIF(SUM(conversions), 0) > 40.0 THEN '❌ HIGH CAC: Check Funnel'
        ELSE '✅ PROFITABLE: Scale Up'
    END AS campaign_health_status

FROM marketing_campaigns
WHERE campaign_date BETWEEN '2026-04-01' AND '2026-04-30'
GROUP BY channel
ORDER BY roas DESC;
```

## 3. Technical Command Breakdown
* **`SUM()`**: Aggregates daily rows into a single monthly total for each channel.
* **`NULLIF(..., 0)`**: Prevents system-crashing "divide-by-zero" errors if a campaign recorded $0 spend.
* **`ROUND(..., 2)`**: Formats numbers into a clean currency layout with two decimal places.
* **`CASE WHEN`**: Evaluates conditions to automatically output diagnostic text alerts.

## 4. BI Dashboard Transformation Strategy
1. **Connect:** Use the "Custom SQL" feature in Tableau or Power BI and paste the query above.
2. **KPI Cards:** Place large text callouts for `total_spend` and `total_revenue` at the top left.
3. **Bar Chart:** Map `channel` to the Y-axis and `roas` to the X-axis, with a line marker at `1.0`.
4. **Color Strategy:** Apply a muted brand color for `PROFITABLE` rows and an alert color (like soft red) strictly for `LOSSMAKER` channels.
