# Points chauds

| # | Question | Impact si on se trompe | Qui peut répondre | Devient un ADR ? |
|---|---|---|---|---|
| 1 | « Alerte » : un ou deux sens ? | Deux cycles de vie mélangés dans un seul modèle | Équipe (décidé : deux sens) | non (glossaire) |
| 2 | Fréquence des mesures : 30 min (UC-T02) ou 15 min (38 400 mesures/jour correspond à 15 min) ? | Volumétrie et seuils faux | BeeAfrica / enseignant | non (paramètre) |
| 3 | Retard maximal accepté d'une mesure en mode dégradé ? | Alertes en temps réel sur des données périmées | BeeAfrica | oui |
| 4 | Seuil d'écart de rendement : 20 % ? par coopérative ? | Alertes inutiles ou manquées | Coordinateurs BeeAfrica | non (paramètre) |
| 5 | Seuil d'escalade de 48 h : fixé par qui, et où vit la politique (tâche planifiée, file différée, base) ? | Escalades ratées ou trop tardives | BeeAfrica / équipe | oui |
| 6 | Plages physiques valides (poids max, températures) : qui les fixe ? | Rejets abusifs ou données aberrantes acceptées | BeeAfrica (apiculteurs) | non (paramètre) |
| 7 | Variation brutale de poids : signal de risque ou erreur de capteur ? | Fausses alertes ou essaimage manqué | BeeAfrica / équipe Data | oui |
| 8 | Tolérance GPS de « position hors tolérance » (UC-CY04) ? | Fausses alertes de déplacement | BeeAfrica | non (paramètre) |
| 9 | Comment l'application terrain hors ligne reçoit-elle les alertes (push, synchronisation, file locale) ? | Alerte non vue par l'apicultrice | Équipe | oui |
| 10 | Ingestion des mesures : traitement par lots ou en flux ? | Latence des alertes et robustesse en connexion intermittente | Équipe | oui |
