# [TTS] Shower Time

## Description

Automatisation qui annonce l'heure de la douche avec un message TTS dynamique et ajoute un fun fact aléatoire. Elle ne se déclenche que certains jours et uniquement si tout le monde est à la maison et que l'interrupteur logique `input_boolean.shower` est activé.

## Déclencheur

- Horaire défini par `input_datetime.shower_time` (déclenchement exact à l'heure configurée)

## Conditions

Toutes doivent être vraies :

1. `input_boolean.shower` = `on` (autorisation douche)
2. `group.persons` = `home` (toutes les personnes présentes)
3. Jour de la semaine ∈ {dimanche, mardi, jeudi}

## Actions

1. Appel du script `script.facts_fun_fact_aleatoire` pour récupérer un fait aléatoire (`response_variable: fact_result`)
2. Définition des variables :
   - `time` = heure courante `%H:%M`
   - `message` = sélection aléatoire parmi 3 formulations d'annonce
3. Lecture TTS via `tts.google_cloud` sur `media_player.toutes_les_enceintes` avec :
   - Message principal (annonce douche)
   - Phrase "Mais d'abord" suivie du fun fact (`fact_result.fun_fact`)

## Entités utilisées

- `input_datetime.shower_time`
- `input_boolean.shower`
- `group.persons`
- `media_player.toutes_les_enceintes`
- `tts.google_cloud`
- `script.facts_fun_fact_aleatoire`

## Exemple de message

> Il est 19:30. C'est l'heure de la douche !\n\nMais d'abord\n\nLe saviez-vous : Les pieuvres ont trois cœurs.

## Paramètres ajustables

- Modifier l'heure dans `input_datetime.shower_time`
- Activer/désactiver la fonctionnalité via `input_boolean.shower`
- Ajuster les jours autorisés dans la condition `weekday`
- Ajouter ou retirer des phrases dans la liste `message`

## Mode

`single` : évite les chevauchements de lecture si relancé.

## État

✅ Actif
