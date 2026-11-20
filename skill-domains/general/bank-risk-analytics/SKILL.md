---
name: bank-risk-analytics
description: générer, valider, exécuter et expliquer dynamiquement des requêtes sql teradata gouvernées pour l'analyse risque et finance du jeu de données bancaire rf_biz. utiliser pour les questions en langage naturel sur le coût du risque, l'ecl, l'ead, le coefficient d'exploitation, les revenus, les charges, les rwa, la concentration client, les allocations de coûts, les comparaisons temporelles, les classements, les tendances, les écarts et les ventilations par pays, filiale, produit, secteur, client ou période.
---
# Mission

Transformer une question analytique en langage naturel en une requête Teradata SQL dynamique, gouvernée et explicable. Adapter les dimensions, filtres, périodes, comparaisons, classements et limites à la question. Ne jamais sélectionner une requête complète prédéfinie simplement parce qu'elle ressemble à un exemple.

## Ressources obligatoires

Charger uniquement les références utiles à la question :

- `references/metrics.yaml` pour les définitions des métriques, formules, unités et dimensions compatibles.
- `references/semantic-model.yaml` pour choisir une vue, vérifier son grain et ses colonnes.
- `references/joins.yaml` uniquement si une jointure est nécessaire.
- `references/time-semantics.md` pour interpréter les périodes et comparaisons.
- `references/sql-generation-rules.md` avant de produire ou exécuter du SQL.
- `references/analysis-patterns.md` pour les analyses de variation, concentration, classement et tendance.
- `references/lineage.yaml` pour toute question de provenance, de calcul ou de lineage.
- `references/visualization-rules.md` pour recommander ou générer une visualisation.
- `references/examples.md` uniquement comme démonstration du raisonnement paramétrique, jamais comme catalogue de SQL à recopier.

# Workflow obligatoire

## 1. Construire le plan analytique

Extraire silencieusement de la question :

```yaml
intent: aggregate | compare | trend | rank | explain_variance | concentration | lineage | detail
metrics: []
dimensions: []
filters: {}
time_window: {}
comparison: null
order_by: []
limit: null
requested_unit: null
```

Résoudre les synonymes avec `metrics.yaml`. Ne pas exécuter de SQL tant que la métrique et le grain ne sont pas déterminés.

## 2. Résoudre les ambiguïtés

- Si une seule interprétation gouvernée est raisonnable, l'utiliser et indiquer l'hypothèse dans la réponse.
- Demander une précision seulement si l'ambiguïté change matériellement le résultat, par exemple coût du risque en EUR ou en points de base.
- Utiliser les valeurs par défaut de la métrique lorsqu'aucune période ou unité n'est précisée.
- Ne jamais inventer une filiale, un pays, une colonne, une table ou une relation.

## 3. Choisir la surface de données

1. Préférer une vue `RF_BIZ` compatible avec la métrique, les dimensions et le grain.
2. Utiliser `RF_CORE` uniquement si le détail demandé n'existe pas dans `RF_BIZ`.
3. Utiliser `RF_WRK` ou `RF_STG` uniquement pour une explication de calcul, une réconciliation ou un lineage explicite.
4. Sélectionner la surface la plus détaillée requise, mais pas plus détaillée.
5. Ne faire une jointure que si elle est déclarée dans `joins.yaml`.

Si les bases de données `RF_BIZ`,`RF_CORE`, `RF_WRK` et `RF_STG` n'existent pas, utilise la base de données `ADLDSD_OFS_NEO`, ou demande à l'utilisateur de spécifier le nom d'une base de données. Bien qu'une seule base n'est utilisée, les objets possèdent un préfixe qui identifient leur type :
1. Les vues dans RF_BIZ ont le préfixe `vw_`.
2. Les tables dans `RF_CORE` ont le préfixe `dim_` pour les dimensions, et `fact_` pour les tables de faits.
3. Les tables dans `RF_WRK` ont le préfixe `wrk_`, et les tables dans `RF_STG` ont le préfixe `stg_`

## 4. Composer le SQL dynamiquement

Construire les clauses à partir du plan analytique :

- `SELECT` : dimensions demandées et expressions gouvernées des métriques.
- `FROM` : vue choisie selon le grain.
- `JOIN` : uniquement jointures autorisées.
- `WHERE` : période et filtres explicites.
- `GROUP BY` : dimensions sélectionnées.
- `HAVING` : seuil sur une mesure agrégée.
- `QUALIFY` : fonctions analytiques, classement et sélection de période.
- `ORDER BY` : classement demandé ou tri métier utile.
- `TOP` : limiter les détails ou appliquer le top N demandé.

Ne pas coder en dur un pays, un produit, une période, un seuil ou un top N qui n'est ni demandé ni défini comme valeur par défaut gouvernée.

## 5. Valider avant exécution

Vérifier systématiquement :

- requête en lecture seule ;
- objets et colonnes présents dans le modèle sémantique ;
- métrique calculée exactement selon `metrics.yaml` ;
- dimension compatible avec la métrique ;
- grain sans duplication ;
- jointures et cardinalités autorisées ;
- division protégée par `NULLIFZERO` ;
- calculs décimaux sensibles explicitement convertis pour éviter l'erreur Teradata 2614 ;
- période bornée ;
- absence de `SELECT *` ;
- détail limité à 1 000 lignes par défaut ;
- aucune donnée de staging exposée sans demande de lineage.

## 6. Exécuter et corriger

Lorsque l'outil SQL Teradata est disponible, exécuter la requête validée. Si elle échoue :

1. Identifier la cause technique.
2. Corriger une seule fois sans changer la définition fonctionnelle, la période ou les filtres.
3. Si l'échec persiste, rendre le SQL, l'erreur et une correction précise. Ne pas fabriquer de résultat.

Pour une multiplication ECL, éviter l'accumulation d'échelle `DECIMAL`. Utiliser des conversions intermédiaires contrôlées ou `FLOAT`, puis convertir le montant final en `DECIMAL(18,2)`.

## 7. Analyser le résultat

- Répondre d'abord à la question en une ou deux phrases.
- Quantifier les principaux constats avec unité et période.
- Pour une variation, séparer valeur courante, valeur précédente, variation absolue et variation relative.
- Pour une explication, classer les contributeurs et distinguer corrélation analytique et causalité démontrée.
- Pour un lineage, présenter le chemin complet, les colonnes, la formule, le batch et la version de modèle disponibles.
- Ne jamais tirer une conclusion non soutenue par les résultats.

## 8. Restituer

Utiliser ce format par défaut, en supprimant les sections inutiles :

```markdown
## Réponse
[Réponse directe et chiffrée]

## Principaux constats
[Ventilations, tendances ou contributeurs utiles]

## Périmètre
- Période : ...
- Unité : ...
- Filtres : ...
- Grain : ...

## Requête exécutée
```sql
...
```

## Source et définition
- Vue : ...
- Métrique : ...
- Formule : ...

## Visualisation
[Type de graphique et encodages recommandés]
```

Ne pas afficher le plan analytique interne sauf si l'utilisateur le demande.
