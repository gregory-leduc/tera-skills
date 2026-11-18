# Exemples paramétriques

Ces exemples illustrent la construction. Ne pas recopier leurs filtres ou périodes lorsque la question diffère.

## Exemple 1

Question : « Compare le coût du risque de la France et de l'Espagne par trimestre depuis deux ans. »

Plan :

```yaml
intent: compare
metrics: [cost_of_risk_amount]
dimensions: [quarter_end, country_name]
filters: {country_code: [FR, ES]}
time_window: {type: trailing_completed_months, value: 24}
comparison: time_series
```

Construire le SQL depuis `RF_BIZ.vw_risk_metrics`, grouper par trimestre et pays, sans reprendre un SQL dédié à la période de douze mois.

## Exemple 2

Question : « Quelles sont les cinq filiales dont le coefficient d'exploitation dépasse 60 % au dernier mois ? »

Plan :

```yaml
intent: rank
metrics: [efficiency_ratio]
dimensions: [entity_code, entity_name]
filters: {efficiency_ratio: {operator: ">", value: 60}}
time_window: {type: latest_closed_month}
order_by: [{metric: efficiency_ratio, direction: desc}]
limit: 5
```

Appliquer le seuil dans `HAVING`, car il porte sur une mesure agrégée.

## Exemple 3

Question : « Pourquoi les coûts informatiques alloués au crédit immobilier ont-ils augmenté entre les deux derniers mois ? »

Plan :

```yaml
intent: explain_variance
metrics: [allocated_cost]
dimensions: [allocation_step, cost_pool, driver_name]
filters: {product_code: MORTGAGE, cost_pool: CENTRAL_IT}
time_window: {type: latest_two_closed_months}
comparison: pool_and_rate_effects
```

Décomposer l'écart selon `analysis-patterns.md`. Ne pas conclure à une cause opérationnelle non présente dans les données.
