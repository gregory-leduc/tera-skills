---
name: bank-risk-lineage
description: tracer et expliquer le lineage risque-finance de cette démonstration teradata. utiliser lorsque tera doit répondre sur la provenance ecl, les étapes stg wrk core biz, les transformations, les contrôles ou la cascade d allocation.
---
# Workflow
1. Identifier le KPI ou la colonne cible.
2. Lire `references/lineage.md`.
3. Remonter chaque arête de BIZ vers CORE, WRK puis STG.
4. Citer les objets, colonnes, formule, batch, version de modèle et contrôles.
5. Signaler toute rupture ou métadonnée absente.

Ne jamais inventer une source. Pour ECL Espagne, utiliser `RF_BIZ.vw_ecl_lineage_es`.
