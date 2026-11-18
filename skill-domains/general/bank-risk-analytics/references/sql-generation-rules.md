# Règles de génération Teradata SQL

## Sécurité

- Générer uniquement des instructions `SELECT` ou `WITH ... SELECT`.
- Interdire `INSERT`, `UPDATE`, `DELETE`, `MERGE`, `CREATE`, `REPLACE`, `DROP`, `ALTER`, `CALL`, `GRANT` et `REVOKE`.
- Ne pas utiliser `SELECT *`.
- Limiter un résultat de détail à `TOP 1000` par défaut.

## Agrégation

- Grouper par les dimensions sélectionnées.
- Calculer un ratio à partir des sommes de ses composants, jamais comme moyenne de ratios de sous-groupes.
- Ne pas sommer une mesure de snapshot sur plusieurs mois sauf si la métrique le prévoit explicitement. Pour EAD et RWA sur une période, utiliser le dernier snapshot, une moyenne mensuelle ou une série temporelle selon la question.
- Pour une concentration, agréger d'abord l'EAD au niveau client dans le périmètre, classer ensuite les clients, puis calculer le ratio top N.

## Syntaxe Teradata

- Utiliser `NULLIFZERO(denominator)` pour protéger les divisions.
- Utiliser `QUALIFY` pour filtrer le résultat d'une fonction analytique.
- Ne pas réutiliser un alias de colonne dans une autre expression du même `SELECT` si la résolution peut être ambiguë ; utiliser une CTE.
- Utiliser des alias de tables courts et explicites.
- Préférer les noms de colonnes dans `GROUP BY` pour un SQL lisible ; les positions sont acceptées uniquement pour une requête très courte.
- Pour les expressions `DECIMAL` complexes, contrôler la précision. Pour ECL : convertir les facteurs en `FLOAT` pendant la multiplication puis convertir le résultat agrégé en `DECIMAL(18,2)`, ou appliquer des casts décimaux intermédiaires compatibles avec le schéma.

## Filtres

- Échapper les littéraux texte.
- Utiliser les codes gouvernés lorsque le libellé de l'utilisateur correspond sans ambiguïté, par exemple Espagne -> `country_code = 'ES'` et crédit immobilier -> `product_code = 'MORTGAGE'`.
- Si plusieurs valeurs correspondent, interroger les valeurs distinctes disponibles ou demander une précision.
- Ne pas appliquer de filtre implicite sur un pays, une filiale ou un produit.

## Validation du grain

Avant exécution, vérifier que chaque ligne source contribue une seule fois au grain final. Si une jointure many-to-many serait nécessaire, agréger préalablement chaque côté ou refuser la combinaison en expliquant le risque de double comptage.
