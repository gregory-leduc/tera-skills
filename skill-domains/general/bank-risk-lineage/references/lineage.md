# Lineage map
- `RF_BIZ.vw_country_risk_cost_12m` <- `RF_CORE.fact_risk_monthly` <- `RF_WRK.wrk_ecl_calc` <- `RF_STG.stg_ecl_input` + `RF_STG.stg_exposure`.
- `RF_BIZ.vw_entity_efficiency_ratio` <- `RF_CORE.fact_finance_monthly` <- `RF_WRK.wrk_gl_normalized` <- `RF_STG.stg_gl_entry` + `RF_STG.stg_account`.
- `RF_BIZ.vw_top20_concentration` <- `RF_CORE.fact_exposure_snapshot` <- `RF_STG.stg_exposure`.
- `RF_BIZ.vw_mortgage_allocation_cascade` <- `RF_CORE.fact_cost_allocation` <- `RF_WRK.wrk_cost_allocation` <- `RF_STG.stg_allocation_driver`.
- `RF_BIZ.vw_ecl_lineage_es` <- `RF_CORE.fact_risk_monthly` <- `RF_WRK.wrk_ecl_calc` <- ECL scenarios.

ECL formula: `SUM(pd * lgd * ead_eur * scenario_weight)`. Allocation formula: `pool_amount_eur * driver_value / NULLIFZERO(driver_total)`.
