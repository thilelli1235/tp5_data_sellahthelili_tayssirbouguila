# Glossaire QueenBee · groupe data-1

| Terme | Définition validée | Contexte | Dans le code | Source |
|---|---|---|---|---|
| Alerte (sur une colonie) | Signal levé quand le modèle détecte un risque pour une colonie (type de risque, confiance, recommandation). Cycle : levée, prise en charge, résolue, ou escaladée après 48 h. | Santé des colonies | `AlerteColonie` | UC-A02 |
| Alerte (infrastructure) | Signal sur le matériel ou les données : passerelle muette, brouillage, trame rejetée en série, déplacement non déclaré. Renommée « Incident » pour ne pas la confondre. | Supervision et sécurité | `IncidentInfrastructure` | UC-CY01 à UC-CY04 |
| Incident | Événement d'infrastructure ouvert par une politique, journalisé, suivi jusqu'à sa clôture. | Supervision et sécurité | `Incident` | UC-CY01 à UC-CY04 |
| Ruche | Unité inventoriée avec un identifiant PPSITE-NNN, un site, un capteur et une apicultrice affectée. | Référentiel ruchers | `Ruche` | UC-AD01, UC-C01 |
| Site (rucher) | Lieu physique regroupant des ruches et au moins une passerelle. | Référentiel ruchers | `Site` | UC-AD01 |
| Site (périmètre de droits) | Périmètre sur lequel un rôle est attribué à un compte. | Supervision et sécurité | `PerimetreSite` | UC-AD01 |
| Capteur | Appareil fixé à une ruche qui mesure poids, températures, humidité et activité acoustique. | Référentiel ruchers | `Capteur` | UC-T02 |
| Passerelle | Équipement LoRaWAN d'un site qui reçoit les trames des capteurs et les transmet au central, souvent en différé. | Supervision et sécurité | `Passerelle` | UC-T02, UC-CY01 |
| Trame | Message brut reçu d'un capteur, avant toute validation. | Collecte des mesures | `Trame` | UC-T02, UC-CY02 |
| Mesure validée | Mesure d'une ruche à une date, dont la plage, l'unicité et l'horodatage ont été vérifiés ; seule forme de donnée publiée vers les autres contextes. | Collecte des mesures | `MesureValidee` | UC-T02 |
| Validation (d'une mesure) | Contrôle technique d'une trame : ruche connue, valeurs dans la plage physique, pas de rejeu. | Collecte des mesures | `trame.valider()` | UC-CY02 |
| Validation (d'une récolte) | Acceptation de la déclaration par le coordinateur, après comparaison aux poids mesurés. | Production | `recolte.valider()` | UC-C02 |
| Rejeu | Trame déjà reçue (même ruche, même date) ; rejetée et journalisée. | Collecte des mesures | `TrameRejetee` | UC-CY02 |
| Mode dégradé | Fonctionnement d'un site quand sa passerelle est hors ligne : cache local, resynchronisation au retour. | Supervision et sécurité | `ModeDegrade` | UC-CY01 |
| Prédiction de risque | Estimation par le modèle d'un risque (essaimage, affaiblissement) pour une ruche, avec un niveau de confiance. | Prévision | `PredictionRisque` | UC-A02 |
| Prédiction de rendement | Volume attendu calculé par le modèle pour une ruche et une saison. | Prévision | `PredictionRendement` | UC-A03 |
| Score de santé | Indicateur calculé à partir des mesures validées pour classer les ruches par risque. | Santé des colonies | `ScoreSante` | UC-A02, UC-C01 |
| Prise en charge | Engagement d'une apicultrice à traiter une alerte colonie. | Santé des colonies | `alerte.assigner()` | UC-A02 |
| Escalade | Passage d'une alerte colonie à l'état escaladé après 48 h sans prise en charge. | Santé des colonies | `alerte.escalader()` | UC-A02 |
| Visite | Passage planifié d'une apicultrice sur une ruche, assigné par le coordinateur. | Interventions terrain | `Visite` | UC-C01 |
| Inspection | Constat sur le terrain (état visuel, reine, comportement, photo), possible hors ligne. | Interventions terrain | `Inspection` | UC-A01 |
| Récolte | Volume de miel déclaré pour une ruche, à une date. | Production | `Recolte` | UC-A03 |
| Écart de rendement | Différence, en %, entre la récolte déclarée et la prédiction. | Production | `ecartRendement` | UC-A03, UC-C02 |
| Rapport d'impact | Document PDF d'indicateurs sur une période, des sites et des indicateurs choisis, destiné au bailleur. | Impact et reporting | `RapportImpact` | UC-G02 |
| Déplacement (transhumance) | Changement de position d'une ruche, déclaré ou non ; non déclaré, il déclenche un incident prioritaire. | Référentiel ruchers | `Deplacement` | UC-CY04 |

## Mots à plusieurs sens

- « Alerte » : sens 1 dans Santé des colonies (risque sur une colonie, avec prise en charge et escalade à 48 h) ; sens 2 dans Supervision et sécurité (problème d'infrastructure ou d'intégrité). Décision : deux lignes, deux noms dans le code (`AlerteColonie` et `IncidentInfrastructure`), car les acteurs, les règles et le cycle de vie diffèrent.
- « Site » : sens 1 dans Référentiel ruchers (rucher physique) ; sens 2 dans Supervision et sécurité (périmètre de droits d'un rôle). Décision : deux lignes, le second s'appelle `PerimetreSite` dans le code.
- « Validation » : sens 1 dans Collecte des mesures (contrôle technique d'une trame) ; sens 2 dans Production (acceptation d'une récolte par le coordinateur). Décision : deux lignes, deux méthodes distinctes (`trame.valider()` et `recolte.valider()`).
- « Mesure » : une trame est brute et ne sort jamais de Collecte ; une mesure validée est la seule forme publiée. Décision : on n'utilise jamais « mesure » seul.
