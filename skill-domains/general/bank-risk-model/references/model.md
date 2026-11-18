# Proposed data model

## Layering
- **STG**: source-shaped ingestion tables with batch and source identifiers.
- **WRK**: normalized, enriched and calculated intermediate objects.
- **CORE**: conformed dimensions and auditable monthly facts.
- **BIZ**: governed business views used by Tera and dashboards.
- **META**: lineage, KPI, business glossary and data-quality metadata.

## Objects
| Layer | Object | Purpose | Grain |
|---|---|---|---|
| STG | stg_entity | Legal entities and ownership | entity |
| STG | stg_account | Analytical chart of accounts | account |
| STG | stg_gl_entry | Monthly management-accounting entries | entity, month, account, product |
| STG | stg_exposure | Client exposures and collateral | client, entity, month, facility |
| STG | stg_ecl_input | PD, LGD, EAD, stage and scenario weights | facility, month, scenario |
| STG | stg_allocation_driver | Cost-pool allocation drivers | pool, receiver, month, step |
| WRK | wrk_gl_normalized | Currency-normalized and signed ledger | entry |
| WRK | wrk_exposure_enriched | Exposure joined to client, sector, product | facility, month |
| WRK | wrk_ecl_calc | Scenario-weighted ECL calculation | facility, month |
| WRK | wrk_cost_allocation | Multi-step allocation cascade | pool, sender, receiver, month, step |
| CORE | dim_date | Month dimension | month |
| CORE | dim_country | Country and region | country |
| CORE | dim_entity | Legal entity / subsidiary | entity |
| CORE | dim_sector | Client sector | sector |
| CORE | dim_client | Synthetic group/client | client |
| CORE | dim_product | Banking product | product |
| CORE | dim_account | P&L and risk account taxonomy | account |
| CORE | fact_finance_monthly | Revenue, operating expenses, allocated costs | entity, month, product, account |
| CORE | fact_risk_monthly | ECL and cost of risk | entity, month, product |
| CORE | fact_exposure_snapshot | EAD, RWA, collateral and outstanding | client, entity, month, product |
| CORE | fact_cost_allocation | Explainable allocation cascade | sender, receiver, month, step, pool |
| BIZ | vw_country_risk_cost_12m | Cost of risk by country, trailing 12 months | country |
| BIZ | vw_entity_efficiency_ratio | Cost/income ratio by subsidiary | entity, month |
| BIZ | vw_top20_concentration | Top-20 exposure concentration | country, sector, month |
| BIZ | vw_mortgage_allocation_cascade | Drivers of mortgage allocated-cost changes | entity, month, step, pool |
| BIZ | vw_ecl_lineage_es | Spanish subsidiary ECL provenance | facility, month |
| BIZ | vw_exec_finance_risk_dashboard | Dashboard-ready KPI surface | entity, month |
| META | meta_asset | Catalog of demo objects | asset |
| META | meta_lineage_edge | Column/object lineage edges and transformation logic | edge |
| META | meta_business_term | Functional definitions and synonyms | term |
| META | meta_kpi_definition | Governed formulas, filters and owners | KPI |
| META | meta_data_quality_rule | Controls and thresholds | rule |
