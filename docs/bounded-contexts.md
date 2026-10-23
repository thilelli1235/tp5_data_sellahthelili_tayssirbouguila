# Bounded contexts QueenBee

## Référentiel ruchers
- Responsabilité (une phrase) : Tient l'inventaire des sites, des ruches, des capteurs associés et des affectations aux apicultrices.
- Type : support · spécialité la plus concernée : Dev
- Agrégats : Site (code unique, un seul pays) ; Ruche (identifiant PPSITE-NNN unique, un seul site, un seul capteur associé à la fois)
- Événements publiés : SiteEnregistré.v1, RucheEnregistrée.v1, RucheAffectée.v1, DéplacementDéclaré.v1
- Événements consommés : aucun (alimenté par des commandes de l'administrateur et du coordinateur)
- Cas d'utilisation sources : UC-AD01 (en partie), UC-C01, UC-CY04 (déclaration de déplacement)
- Mots propres à ce contexte : Ruche (objet inventorié avec un identifiant), Site (rucher physique), Capteur
- Pourquoi une frontière ici : Ici « Site » désigne un lieu physique avec des ruches ; dans Supervision et sécurité il désigne un périmètre de droits.

## Collecte des mesures
- Responsabilité (une phrase) : Reçoit les trames des passerelles, les valide (plage, rejeu, retard) et publie des mesures fiables.
- Type : support · spécialité la plus concernée : Data
- Agrégats : Trame (clé hive_id + timestamp unique ; une trame rejetée n'est jamais validée ; une mesure validée est immuable)
- Événements publiés : MesureValidée.v1, TrameRejetée.v1, MesureMarquéeTardive.v1
- Événements consommés : RucheEnregistrée.v1 (venant de Référentiel ruchers) ; trames LoRaWAN brutes (venant des capteurs et passerelles, via ACL)
- Cas d'utilisation sources : UC-T02, UC-CY02
- Mots propres à ce contexte : Trame (donnée brute reçue), Mesure validée (donnée publiée), Rejeu, Retard
- Pourquoi une frontière ici : Le vocabulaire est celui du signal (trame, plage physique, rejeu) ; les autres contextes ne voient que des mesures déjà validées.

## Supervision et sécurité
- Responsabilité (une phrase) : Surveille l'infrastructure (passerelles, capteurs, intégrité des données, déplacements) et gère comptes, rôles et traçabilité.
- Type : générique · spécialité la plus concernée : Cyber
- Agrégats : Incident (un incident clos n'accepte plus de transition ; chaque ouverture est journalisée) ; Passerelle (mode dégradé seulement si hors ligne) ; Compte (bloqué après 5 échecs, rôles par site)
- Événements publiés : IncidentOuvert.v1 (passerelle hors ligne, brouillage, déplacement non déclaré, intégrité), ModeDégradéActivé.v1, PasserelleRétablie.v1, CompteDésactivé.v1
- Événements consommés : TrameRejetée.v1 (venant de Collecte) ; MesureValidée.v1 pour la position (venant de Collecte) ; DéplacementDéclaré.v1 (venant de Référentiel) ; battements de cœur (passerelles, via ACL)
- Cas d'utilisation sources : UC-AD01, UC-T01, UC-CY01, UC-CY02, UC-CY03, UC-CY04
- Mots propres à ce contexte : Incident (alerte d'infrastructure), Mode dégradé, Battement de cœur, Site (périmètre de droits)
- Pourquoi une frontière ici : Ici « alerte » parle de l'infrastructure, avec d'autres acteurs (administrateur) et un autre cycle de vie que l'alerte sur une colonie.

## Prévision
- Responsabilité (une phrase) : Calcule les risques (essaimage, affaiblissement) et les rendements attendus à partir des mesures et de la météo.
- Type : support · spécialité la plus concernée : Data
- Agrégats : Prédiction (confiance comprise entre 0 et 1 ; type de risque dans une liste fermée)
- Événements publiés : RisqueDétecté.v1, PrédictionRendementCalculée.v1
- Événements consommés : MesureValidée.v1 (venant de Collecte) ; RécolteValidée.v1 (venant de Production, pour recalibrer) ; météo (OpenWeatherMap, via ACL)
- Cas d'utilisation sources : UC-A02, UC-A03
- Mots propres à ce contexte : Prédiction de risque, Prédiction de rendement, Confiance
- Pourquoi une frontière ici : Le modèle parle en probabilités et en confiance ; les autres contextes parlent en faits décidés.

## Santé des colonies
- Responsabilité (une phrase) : Transforme un risque détecté en alerte sur une colonie et suit sa prise en charge jusqu'à sa résolution.
- Type : cœur · spécialité la plus concernée : Dev
- Agrégats : AlerteColonie (LEVÉE → PRISE_EN_CHARGE → RÉSOLUE, ou ESCALADÉE après 48 h ; une alerte résolue n'accepte plus de transition ; confiance entre 0 et 1)
- Événements publiés : AlerteLevée.v1, AlertePriseEnCharge.v1, AlerteEscaladée.v1, AlerteRésolue.v1
- Événements consommés : RisqueDétecté.v1 (venant de Prévision) ; MesureValidée.v1 (venant de Collecte) ; InspectionEnregistrée.v1 (venant d'Interventions terrain)
- Cas d'utilisation sources : UC-A02, UC-C01 (classement par risque)
- Mots propres à ce contexte : Alerte (sur une colonie), Prise en charge, Escalade, Score de santé
- Pourquoi une frontière ici : L'alerte colonie a un cycle de vie (levée, prise en charge, escalade, résolution) et des acteurs (apicultrice, coordinateur) qui n'existent pas pour un incident d'infrastructure.

## Interventions terrain
- Responsabilité (une phrase) : Planifie et assigne les visites, enregistre les inspections (y compris hors ligne) et les synchronise.
- Type : support · spécialité la plus concernée : Dev
- Agrégats : Visite (assignée à une seule apicultrice ; une visite réalisée n'est plus replanifiable) ; Inspection (liée à une ruche existante ; immuable une fois synchronisée)
- Événements publiés : VisitePlanifiée.v1, InspectionEnregistrée.v1, AnomalieSignalée.v1
- Événements consommés : AlerteLevée.v1 (venant de Santé des colonies) ; RucheEnregistrée.v1 (venant de Référentiel, en conformiste)
- Cas d'utilisation sources : UC-A01, UC-C01
- Mots propres à ce contexte : Visite, Inspection, Synchronisation, Conflit de synchronisation
- Pourquoi une frontière ici : Le vocabulaire est celui du terrain et du hors-ligne (synchronisation, conflit), absent des autres contextes.

## Production
- Responsabilité (une phrase) : Gère la déclaration des récoltes, leur comparaison à la prédiction et leur validation par le coordinateur.
- Type : cœur · spécialité la plus concernée : Data
- Agrégats : Récolte (volume positif ; une récolte validée ne change plus ; un écart signalé exige un commentaire)
- Événements publiés : RécolteDéclarée.v1, RécolteValidée.v1, ÉcartMisEnInvestigation.v1
- Événements consommés : PrédictionRendementCalculée.v1 (venant de Prévision) ; MesureValidée.v1 pour les poids (venant de Collecte)
- Cas d'utilisation sources : UC-A03, UC-C02
- Mots propres à ce contexte : Récolte, Écart de rendement, Validation (d'une récolte)
- Pourquoi une frontière ici : « Validation » signifie ici l'acceptation d'une déclaration par le coordinateur, pas la validation technique d'une trame.

## Impact et reporting
- Responsabilité (une phrase) : Consolide les indicateurs multi-sites et produit les rapports d'impact pour les bailleurs.
- Type : support · spécialité la plus concernée : Data
- Agrégats : Rapport (période valide, début avant fin ; un rapport généré est immuable)
- Événements publiés : RapportGénéré.v1, RapportEnvoyé.v1
- Événements consommés : AlerteLevée.v1, AlerteRésolue.v1 (Santé des colonies) ; RécolteValidée.v1 (Production) ; IncidentOuvert.v1 (Supervision, pour la couverture réseau)
- Cas d'utilisation sources : UC-G01, UC-G02
- Mots propres à ce contexte : Indicateur, Rapport d'impact, Période de rapport, Bailleur
- Pourquoi une frontière ici : Le vocabulaire est celui de l'agrégation et de la redevabilité (indicateurs, bailleurs), loin du terrain.
