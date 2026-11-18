# Governed SQL patterns
## 1 Cost of risk by country, trailing 12 months
```sql
SELECT * FROM RF_BIZ.vw_country_risk_cost_12m ORDER BY cost_of_risk_12m_eur DESC;
```
Chart: ranked bar, color by cost_of_risk_bps.
## 2 Subsidiaries above 60%
```sql
SELECT * FROM RF_BIZ.vw_entity_efficiency_ratio WHERE efficiency_ratio_pct > 60 QUALIFY month_end=MAX(month_end) OVER();
```
Chart: horizontal bar with a 60% reference line.
## 3 Top-20 concentration
```sql
SELECT * FROM RF_BIZ.vw_top20_concentration QUALIFY month_end=MAX(month_end) OVER() ORDER BY top20_concentration_pct DESC;
```
Chart: country-sector heatmap with drill-through.
## 4 Mortgage allocation increase
```sql
SELECT * FROM RF_BIZ.vw_mortgage_allocation_cascade WHERE mom_change_eur>0 ORDER BY month_end DESC,mom_change_eur DESC;
```
Chart: waterfall by allocation step and cost pool; explain pool, driver, and rate effects.
## 5 Spanish ECL lineage
```sql
SELECT * FROM RF_BIZ.vw_ecl_lineage_es ORDER BY month_end DESC,entity_code,product_code;
```
Visual: lineage path plus ECL trend and model-version evidence.
