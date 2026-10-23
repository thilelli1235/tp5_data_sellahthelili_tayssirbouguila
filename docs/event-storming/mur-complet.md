# Mur Event Storming QueenBee · version texte

59 événements, 27 politiques, 12 agrégats, 8 bounded contexts. ★ = événement pivot.

Ordre du mur : avant la saison (référentiel), en continu (collecte, supervision), quand un risque apparaît (prévision, santé), sur le terrain, à la récolte, en fin de période (impact).


## Référentiel ruchers (support)

| # | Événement (orange) | Acteur (jaune pâle) | Commande (bleu) | Système externe (rose) | Agrégat (jaune) | Politique (violet) |
|---|---|---|---|---|---|---|
| 1 | Site enregistré | Administrateur | Enregistrer un site |  | Site |  |
| 2 | Ruche enregistrée | Administrateur | Enregistrer une ruche |  | Ruche |  |
| 3 | Capteur associé à la ruche | Administrateur | Associer un capteur |  | Ruche |  |
| 4 | Ruche affectée à une apicultrice | Coordinateur régional | Affecter une ruche |  | Ruche |  |
| 5 | Déplacement déclaré | Apicultrice | Déclarer un déplacement |  | Ruche |  |

## Collecte des mesures (support)

| # | Événement (orange) | Acteur (jaune pâle) | Commande (bleu) | Système externe (rose) | Agrégat (jaune) | Politique (violet) |
|---|---|---|---|---|---|---|
| 6 | Seuil dépassé |  |  | Capteur |  |  |
| 7 | Trame reçue par la passerelle |  |  | Capteur + passerelle LoRaWAN |  |  |
| 8 | Trame transmise au central |  | Recevoir une trame | Passerelle LoRaWAN | Trame |  |
| 9 | Trame rejetée (hors plage) |  | Rejeter la trame |  | Trame | Quand trame transmise, si hors plage physique, alors rejeter |
| 10 | Trame rejetée (rejeu) |  | Rejeter la trame |  | Trame | Quand trame transmise, si déjà reçue, alors rejeter |
| 11 | ★ Mesure validée |  | Valider la mesure |  | Trame | Quand trame transmise et valide, alors valider la mesure |
| 12 | Mesure marquée tardive |  | Marquer la mesure tardive |  | Trame | Si retard > retard maximal, alors marquer tardive |

## Supervision et sécurité (générique)

| # | Événement (orange) | Acteur (jaune pâle) | Commande (bleu) | Système externe (rose) | Agrégat (jaune) | Politique (violet) |
|---|---|---|---|---|---|---|
| 13 | Compte créé | Administrateur | Créer un compte |  | Compte |  |
| 14 | Rôles assignés par site | Administrateur | Assigner des rôles |  | Compte |  |
| 15 | Utilisateur authentifié | Tout utilisateur | S'authentifier |  | Compte |  |
| 16 | Connexion échouée | Tout utilisateur | S'authentifier |  | Compte |  |
| 17 | Compte bloqué |  | Bloquer le compte |  | Compte | Quand 5 connexions échouées, alors bloquer le compte |
| 18 | Compte désactivé | Administrateur | Désactiver un compte |  | Compte |  |
| 19 | Passerelle installée | Administrateur | Installer une passerelle |  | Passerelle |  |
| 20 | ★ Passerelle hors ligne détectée |  | Ouvrir un incident |  | Incident | Si aucun battement de cœur depuis 15 min, alors ouvrir un incident |
| 21 | Mode dégradé activé |  | Activer le cache local |  | Passerelle | Quand passerelle hors ligne, alors activer le mode dégradé |
| 22 | Passerelle rétablie |  | Reprendre le service | Passerelle LoRaWAN | Passerelle |  |
| 23 | Resynchronisation terminée |  | Resynchroniser |  | Passerelle | Quand passerelle rétablie, alors resynchroniser |
| 24 | Capteur devenu silencieux |  | Ouvrir un incident |  | Incident | Si aucune mesure depuis 3 périodes, alors ouvrir un incident |
| 25 | Alerte d'intégrité levée |  | Ouvrir un incident |  | Incident | Si trames rejetées répétées, alors lever une alerte d'intégrité |
| 26 | Brouillage détecté |  | Ouvrir un incident |  | Incident | Si taux de trames perdues anormal, alors ouvrir un incident |
| 27 | Changement de canal proposé |  | Proposer un canal |  | Incident | Quand brouillage détecté, alors proposer un changement de canal |
| 28 | ★ Déplacement non déclaré détecté |  | Ouvrir un incident |  | Incident | Si position hors tolérance et non déclarée, alors ouvrir un incident |
| 29 | Alerte prioritaire notifiée |  | Notifier | Service de notification | Incident | Quand déplacement non déclaré, alors notifier coordinateur et administrateur |
| 30 | Incident journalisé |  | Journaliser l'incident |  | Incident | Quand incident ouvert, alors journaliser |

## Prévision (support)

| # | Événement (orange) | Acteur (jaune pâle) | Commande (bleu) | Système externe (rose) | Agrégat (jaune) | Politique (violet) |
|---|---|---|---|---|---|---|
| 31 | Météo récupérée |  | Récupérer la météo | OpenWeatherMap | Prédiction |  |
| 32 | Risque d'essaimage détecté |  | Évaluer le risque | Modèle de prédiction | Prédiction |  |
| 33 | Risque d'affaiblissement détecté |  | Évaluer le risque | Modèle de prédiction | Prédiction |  |
| 34 | Prédiction de rendement calculée |  | Calculer le rendement attendu | Modèle de prédiction | Prédiction |  |

## Santé des colonies (cœur)

| # | Événement (orange) | Acteur (jaune pâle) | Commande (bleu) | Système externe (rose) | Agrégat (jaune) | Politique (violet) |
|---|---|---|---|---|---|---|
| 35 | Score de santé recalculé |  | Recalculer le score |  | (projection) | Quand mesure validée, alors recalculer le score |
| 36 | ★ Alerte levée |  | Lever une alerte |  | AlerteColonie | Quand risque détecté, alors lever une alerte |
| 37 | Apicultrice notifiée |  | Notifier | Service de notification | AlerteColonie | Quand alerte levée, alors notifier (push ou au prochain accès) |
| 38 | Alerte prise en charge | Apicultrice | Prendre en charge l'alerte |  | AlerteColonie |  |
| 39 | Alerte escaladée |  | Escalader l'alerte |  | AlerteColonie | Si aucune prise en charge sous 48 h, alors escalader |
| 40 | Coordinateur notifié de l'escalade |  | Notifier | Service de notification | AlerteColonie | Quand alerte escaladée, alors notifier le coordinateur du site |
| 41 | Alerte résolue |  | Résoudre l'alerte |  | AlerteColonie | Quand inspection enregistrée pour l'alerte, alors résoudre |

## Interventions terrain (support)

| # | Événement (orange) | Acteur (jaune pâle) | Commande (bleu) | Système externe (rose) | Agrégat (jaune) | Politique (violet) |
|---|---|---|---|---|---|---|
| 42 | Visite anticipée demandée | Apicultrice | Demander une visite anticipée |  | Visite |  |
| 43 | Visite planifiée | Coordinateur régional | Planifier une visite |  | Visite |  |
| 44 | Visite assignée | Coordinateur régional | Assigner une visite |  | Visite |  |
| 45 | Inspection enregistrée hors ligne | Apicultrice | Enregistrer une inspection |  | Inspection |  |
| 46 | Inspection synchronisée |  | Synchroniser l'inspection | Application terrain | Inspection |  |
| 47 | Conflit de synchronisation détecté |  | Signaler le conflit |  | Inspection | Quand inspection synchronisée, si version divergente, alors signaler le conflit |
| 48 | Ruche introuvable signalée | Apicultrice | Signaler une ruche introuvable |  | Inspection |  |
| 49 | Coordinateur notifié de l'anomalie |  | Notifier | Service de notification | Inspection | Quand inspection avec anomalie, alors notifier le coordinateur |

## Production (cœur)

| # | Événement (orange) | Acteur (jaune pâle) | Commande (bleu) | Système externe (rose) | Agrégat (jaune) | Politique (violet) |
|---|---|---|---|---|---|---|
| 50 | Récolte déclarée | Apicultrice | Déclarer une récolte |  | Récolte |  |
| 51 | Écart de rendement calculé |  | Comparer à la prédiction |  | Récolte | Quand récolte déclarée, alors comparer à la prédiction |
| 52 | Coordinateur notifié de l'écart |  | Notifier | Service de notification | Récolte | Si écart > seuil, alors notifier le coordinateur |
| 53 | Coopérative notifiée |  | Notifier | Service de notification | Récolte | Quand récolte déclarée, alors notifier la coopérative |
| 54 | ★ Récolte validée | Coordinateur régional | Valider la récolte |  | Récolte |  |
| 55 | Écart mis en investigation | Coordinateur régional | Signaler un écart |  | Récolte |  |

## Impact et reporting (support)

| # | Événement (orange) | Acteur (jaune pâle) | Commande (bleu) | Système externe (rose) | Agrégat (jaune) | Politique (violet) |
|---|---|---|---|---|---|---|
| 56 | Indicateurs consolidés |  | Consolider les indicateurs |  | (projection) | En fin de période, alors consolider les indicateurs |
| 57 | Période de rapport choisie | Gestionnaire | Choisir la période |  | Rapport |  |
| 58 | ★ Rapport d'impact généré | Gestionnaire | Générer le rapport |  | Rapport |  |
| 59 | Rapport envoyé au bailleur | Gestionnaire | Envoyer le rapport |  | Rapport |  |

## Agrégats et invariants

| Agrégat | Contexte | Invariants |
|---|---|---|
| Site | Référentiel ruchers | code unique ; un site appartient à un seul pays |
| Ruche | Référentiel ruchers | identifiant PPSITE-NNN unique ; une ruche appartient à un seul site ; un capteur n'est associé qu'à une seule ruche à la fois |
| Passerelle | Supervision et sécurité | le mode dégradé n'est actif que si la passerelle est hors ligne ; une passerelle appartient à un seul site |
| Compte | Supervision et sécurité | bloqué après 5 échecs de connexion ; un compte désactivé ne peut plus s'authentifier ; les rôles sont attribués par site |
| Trame | Collecte des mesures | clé hive_id + timestamp unique ; une trame rejetée n'est jamais validée ; une mesure validée ne change plus |
| Incident | Supervision et sécurité | un incident clos n'accepte plus de transition ; chaque ouverture d'incident est journalisée |
| Prédiction | Prévision | confiance comprise entre 0 et 1 ; type de risque dans une liste fermée |
| AlerteColonie | Santé des colonies | une alerte résolue n'accepte plus de transition ; escalade seulement après 48 h sans prise en charge ; résolution exige un identifiant d'inspection |
| Visite | Interventions terrain | assignée à une seule apicultrice ; une visite réalisée n'est plus replanifiable |
| Inspection | Interventions terrain | liée à une ruche existante ; immuable une fois synchronisée (sauf résolution de conflit) |
| Récolte | Production | volume positif ; une récolte validée ne change plus ; un écart signalé exige un commentaire |
| Rapport | Impact et reporting | période valide (début avant fin) ; un rapport généré est immuable |

## Écartés (étape B2)

- Miel récolté (doublon)
- Ligne insérée dans harvests (technique)
- Bouton Valider cliqué (interface)
- Données insérées (trop vague)
- Erreur 500 sur l'API (technique)
- Insérer la mesure en base (action technique)

## Points chauds

Voir `docs/points-chauds.md`.
