# [TTS] Sleeping Time

## Description

Automatisation qui gère les rappels du soir pour Eliott : fun fact d'ouverture, puis transition vers le brossage des dents ou l'histoire du soir, avec une pause naturelle entre les deux phrases.

## Objectif

Créer un rituel apaisant : une information amusante ou instructive (fun fact) suivie d'une consigne précise adaptée au moment (dents / histoire). Le ton est rassurant, structurant et ludique.

## Déclencheurs

- `input_boolean.bouton_test` (state) — Test manuel (ID: `StoryTime`)
- Heure configurée `input_datetime.sleeping_time` (time) — Brossage des dents (ID: `ToothTime`)
- Timer avant l'heure de coucher via comparaison template entre `sleeping_time` et `sleeping_time_timer` — Histoire du soir (ID: `StoryTime`)

## Conditions

Doit vérifier :

- `input_boolean.sleeping` = on (rituel activé)
- `group.persons` = home (présence à domicile)

## Actions

1. `script.facts_fun_fact_aleatoire` — Récupère une fun fact (dans `fact_result.fun_fact`).
2. Variables :
   - `time` : heure actuelle HH:MM
   - `message` : phrase adaptée selon le trigger
     - ToothTime → Variantes motivantes pour le brossage
     - StoryTime → Variantes invitant au choix d'un livre
     - Autre → Fallback neutre
3. Diffusion TTS :
   - Via `script.tts_send_tts` sur `media_player.google_home_parents`
   - Message en plusieurs lignes :
     - Ligne 1 : fun fact
     - Ligne vide (pause naturelle)
     - Ligne 2 : consigne (brossage ou histoire)

## Phrases de transition

Brossage (`ToothTime`) exemples :

- "Et maintenant qu'on a appris quelque chose, il est HH:MM, on va se brosser les dents."
- "Allez, on passe au brossage des dents. Il est HH:MM."
- "C'est le moment de se laver les dents, il est HH:MM."
- "On garde le sourire propre ! Il est HH:MM, on brosse les dents."

Histoire (`StoryTime`) exemples :

- "Et maintenant place à l'histoire du soir. Il est HH:MM, choisis un livre."
- "On choisit un livre pour l'histoire. Il est HH:MM."
- "C'est l'heure de l'histoire du soir, il est HH:MM. Quel livre tu prends ?"
- "Installe-toi, il est HH:MM : on choisit un livre pour l'histoire."

## Entités utilisées

- `input_boolean.bouton_test`
- `input_boolean.sleeping`
- `group.persons`
- `input_datetime.sleeping_time`
- `input_datetime.sleeping_time_timer`
- `media_player.google_home_parents`
- `script.facts_fun_fact_aleatoire`
- `script.tts_send_tts`

## Format du message final

```
<fun fact>.

<phrase transition>.
```

(Les points sont ajoutés dans le template, ne pas doubler.)

## Personnalisations possibles

- Ajouter une variante pour "déjà en pyjama" (condition booléenne supplémentaire).
- Détecter un retard (ex: après `sleeping_time + 10min`) et changer le ton.
- Ajouter une musique douce avant l'histoire (service `media_player.play_media`).

## Mode

`single` — Évite les chevauchements.

## Bonnes pratiques observées ici

- Séparation du contenu dynamique (fun fact) et du contexte rituel.
- Utilisation de listes pour la variété sans complexité.
- Pause entre phrases obtenue via bloc multi-lignes.

## Exemple d'appel manuel (Developer Tools)

```yaml
service: automation.trigger
entity_id: automation.tts_sleeping_time
```

## Maintenance

- Ajouter/modifier les phrases dans la liste `message` selon le trigger.
- Mettre à jour les fun facts dans le script source si besoin.

---

Pour toute évolution (pyjama, lumière, ambiance), créer des conditions supplémentaires dans le bloc `variables` avant le message.
