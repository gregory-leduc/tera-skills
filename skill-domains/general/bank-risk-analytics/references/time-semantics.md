# Sémantique temporelle

Utiliser la dernière date clôturée disponible dans la surface sélectionnée, jamais `CURRENT_DATE` seul, pour éviter une période sans données.

## Règles

- **Dernier mois** : `month_end = (SELECT MAX(month_end) FROM <surface>)`.
- **N derniers mois** : inclure les N dates mensuelles clôturées jusqu'au maximum disponible. Préférer une sous-requête classant les mois distincts plutôt qu'une approximation calendaire si des mois peuvent manquer.
- **12 derniers mois** : douze mois clôturés, maximum inclus.
- **Cette année / YTD** : du 1er janvier de l'année de la dernière clôture jusqu'à cette clôture.
- **Dernier trimestre** : dernier trimestre entièrement clôturé. Si les données ne permettent pas d'identifier la clôture complète, l'indiquer.
- **Depuis deux ans** : vingt-quatre mois clôturés par défaut.
- **Évolution / tendance** : produire un point par mois, sauf demande de trimestre ou d'année.
- **Augmentation / diminution** : comparer la période courante à la période précédente de même durée.
- **MoM** : mois clôturé le plus récent contre le mois précédent.
- **QoQ** : dernier trimestre clôturé contre le trimestre précédent.
- **YoY** : période contre la même période de l'année précédente.

## Sorties de comparaison

Produire au minimum :

- valeur de la période courante ;
- valeur de la période précédente ;
- variation absolue ;
- variation relative en pourcentage avec `NULLIFZERO`.
