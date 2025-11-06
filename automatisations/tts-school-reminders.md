# [TTS] School Start & End

## Description

Automatisation pour envoyer des rappels TTS (Text-to-Speech) avant le début et la fin de l'école. Les messages de début d'école incluent des faits éducatifs variés (sciences, nature, transports, espace, santé) avec rotation quotidienne déterministe.

## Déclencheurs

### Trigger de test

- **Bouton test** : `input_boolean.bouton_test` avec ID aléatoire pour tester tous les scénarios

### Départ école

- **Timer personnalisé** : Configurable via `input_datetime.school_start_timer` (ID: `start_timer`)
- **10 minutes avant** : Alerte pour se préparer (ID: `start_10min`)
- **5 minutes avant** : Rappel final avant le départ (ID: `start_5min`)

### Fin école

- **Timer personnalisé** : Configurable via `input_datetime.school_end_timer` (ID: `end_timer`)
- **10 minutes avant** : Première alerte (ID: `end_10min`)
- **5 minutes avant** : Rappel pour aller chercher l'enfant (ID: `end_5min`)

## Conditions

L'automatisation ne se déclenche que si :

- `input_boolean.school` est activé (ON)
- `group.persons` est à la maison (home)

## Actions

### Variables définies

- `time` : Heure actuelle formatée (HH:MM)
- `sncf` : Booléen (true pour les déclencheurs de début d'école, incluant les tests aléatoires start\_\*)
- `random_test_id` : ID aléatoire sélectionné pour le trigger test
- `message` : Message TTS sélectionné selon le déclencheur avec rotation quotidienne déterministe

### Messages TTS

#### Début d'école (start_timer, start_10min, start_5min)

Les messages sont des **faits éducatifs** avec rotation quotidienne déterministe basée sur le jour de l'année :

- **26 faits** pour `start_timer` (voitures, espace, nature, corps humain, transports, etc.)
- **15 faits** pour `start_10min` (thèmes similaires avec variations)
- **16 faits** pour `start_5min` (focus énergie, biologie, géographie)

**Thèmes couverts** : Formule 1, voitures électriques, TGV, avions, espace, animaux, corps humain, plantes, énergie, sciences.

**Rotation** : Chaque jour un nouveau fait est sélectionné via `day % list_length` avec offsets différents par trigger pour maximiser la variété.

#### Fin d'école (end_timer, end_10min, end_5min)

Messages concis avec gradation d'urgence :

- **Timer** : "Il est HH:MM. Va chercher Eliott tout de suite."
- **5 min avant** : "Il est HH:MM. Tu dois partir maintenant pour être à l'heure."
- **10 min avant** : "Il est HH:MM. Prépare-toi à partir chercher Eliott."

### Script appelé

`script.tts_send_tts` avec les paramètres :

- `media_player: media_player.ni_oscar_ni_parents`
- `message` : Message généré
- `sncf` : Pour jouer le son SNCF avant les messages de départ école

## Mode d'exécution

`single` : Une seule instance peut s'exécuter à la fois

## Entités référencées

- `input_boolean.bouton_test` : Bouton pour tester l'automatisation avec trigger aléatoire
- `input_datetime.school_start_today` : Heure de début d'école du jour
- `input_datetime.school_end_today` : Heure de fin d'école du jour
- `input_datetime.school_start_timer` : Durée du timer avant le départ
- `input_datetime.school_end_timer` : Durée du timer avant la fin
- `input_boolean.school` : Active/désactive l'automatisation
- `group.persons` : Groupe de personnes pour vérifier la présence
- `media_player.ni_oscar_ni_parents` : Enceinte pour diffuser les messages

## Notes techniques

- **Rotation déterministe** : Utilise `now().timetuple().tm_yday` (jour de l'année) pour sélectionner les faits
- **Offsets** : Chaque trigger utilise un offset différent (`day`, `day+1`, `day+2`) pour varier les faits au sein d'une même matinée
- **Trigger test** : L'ID `test_random` sélectionne aléatoirement un des 6 scénarios via la variable `random_test_id`
- **Son SNCF** : Activé uniquement pour les messages de début d'école (`start_*`)
- **Niveau adapté** : Faits simplifiés pour un enfant de 8 ans avec chiffres et comparaisons concrètes
