# Patterns analytiques

## Classement et top N

1. Agréger la métrique au grain demandé.
2. Classer sur la métrique agrégée.
3. Appliquer `TOP n` ou `ROW_NUMBER() ... QUALIFY` selon le besoin.
4. Conserver les ex æquo uniquement si la question le justifie.

## Concentration client

1. Filtrer le mois et le périmètre.
2. Agréger l'EAD par client et par dimensions de partition.
3. Calculer `ROW_NUMBER()` par EAD décroissante.
4. Calculer l'EAD top N et l'EAD totale dans la même partition.
5. Retourner montant top N, montant total et pourcentage.

## Tendance

Retourner le temps, la métrique et les dimensions de série. Ne pas mélanger les niveaux mensuel, trimestriel et annuel dans le même résultat.

## Analyse de variation

Retourner : valeur courante, valeur précédente, écart absolu et écart relatif. Classer les contributeurs par valeur absolue de leur contribution.

## Cascade d'allocation

Pour expliquer l'évolution d'une charge allouée entre deux périodes :

1. Comparer le montant total alloué.
2. Ventiler par `allocation_step`, `cost_pool` et `driver_name`.
3. Calculer l'effet pool à poids antérieur : `(pool_current - pool_previous) * allocation_rate_previous`.
4. Calculer l'effet taux/mix au pool courant : `pool_current * (allocation_rate_current - allocation_rate_previous)`.
5. Vérifier que `effet_pool + effet_taux_mix` réconcilie la variation, à l'arrondi près.
6. Présenter les principaux contributeurs dans un waterfall.

Ne pas qualifier un facteur de cause métier démontrée si le résultat montre seulement une contribution arithmétique.

## Lineage

Commencer par l'actif cible, parcourir les arêtes vers les sources et restituer pour chaque étape : actif, colonne, transformation, contrôle, identifiant de batch et version de modèle lorsqu'ils existent.
