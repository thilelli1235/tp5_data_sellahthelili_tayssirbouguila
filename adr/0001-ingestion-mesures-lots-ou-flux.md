# ADR 0001 · Ingestion des mesures : traitement par lots ou en flux ?

- Statut : proposé
- Date : 2026-10-23 · Auteurs : (ton nom)

## Contexte

QueenBee reçoit 38 400 mesures par jour (400 ruches, 1 mesure / 15 min, 1 / 5 min en surveillance renforcée). Les passerelles LoRaWAN transmettent souvent en différé, et un site peut renvoyer des milliers de mesures d'un coup après une panne (UC-CY01). Une mesure qui dépasse un seuil est envoyée immédiatement (UC-T02) et peut déclencher une alerte sur une colonie (UC-A02). Les trames hors plage ou rejouées doivent être rejetées et journalisées (UC-CY02). Point chaud d'origine : n°3 (retard maximal) et n°10 (lots ou flux).

## Options envisagées

1. **Lots** : un traitement planifié (toutes les 15 min) lit les trames reçues et publie les mesures validées. Avantages : simple, peu coûteux, facile à rejouer. Inconvénients : latence d'alerte jusqu'à 15 min, pic de charge à chaque lot.
2. **Flux** : chaque trame est validée et publiée dès sa réception (file de messages puis consommateur). Avantages : latence faible pour les alertes, charge lissée. Inconvénients : plus de composants à exploiter, gestion de l'ordre et des doublons à soigner.
3. **Hybride** : flux pour la validation et la publication, lot nocturne pour la réconciliation (doublons, trames manquantes). Avantages : latence faible et robustesse. Inconvénients : deux mécanismes à maintenir.

## Décision

À confirmer en séance 6. Piste : option 3, parce que le volume est faible (moins de 1 mesure/s en moyenne) mais que la latence des alertes et les rattrapages après panne comptent.

## Conséquences

Collecte doit garantir l'idempotence (clé hive_id + timestamp) et publier `occurredAt` et `receivedAt`. Il faudra surveiller le retard d'ingestion et la taille de la file au moment d'un rattrapage.
