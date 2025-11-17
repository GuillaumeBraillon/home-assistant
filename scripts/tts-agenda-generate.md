# [TTS] Agenda Générateur IA

## Description

Script réutilisable qui construit un prompt d'agenda à partir de plusieurs listes d'événements (famille, personnel, vacances, scolaire, fériés) puis appelle `ai_task.generate_data`. Il retourne le texte final via `response_variable: agenda_summary`. Il ne gère ni notification ni lecture TTS pour rester générique.

## Paramètres (data)

- `events_family` (optionnel) : Liste JSON d'événements du calendrier famille
- `events_guillaume` (optionnel) : Événements calendrier personnel Guillaume
- `events_holidays` (optionnel) : Événements jours fériés France
- `events_school` (optionnel) : Événements calendrier scolaire Zone A

Valeur attendue : structure brute `events` renvoyée par `calendar.get_events` (liste). Les blocs absents ou vides sont ignorés.

## Fonctionnement

1. Concatène les segments d'événements présents dans une variable `agenda_instructions` avec règles de style (météo, filtrage, format oral).
2. Appelle `ai_task.generate_data` avec ce prompt.
3. Met le texte généré dans `agenda_summary.data` (accessible par l'automatisation appelante).

## Style du prompt

- Introduction : date du jour + météo `weather.lyon`
- Ignorer événements <= 15 min
- Heures seulement pour événements importants (format 24h)
- Petits événements : mention courte sans heure
- Formulations discrètes pour Metoject / Humira
- Variation des connecteurs pour fluidité

## Exemple d'appel (automation parents)

```yaml
- alias: Script Générateur Agenda Parents
  action: script.tts_agenda_generate
  data:
    events_guillaume: "{{ agenda['calendar.EMAIL_SUPPRIME']['events'] }}"
    events_family: "{{ agenda['calendar.famille']['events'] }}"
  response_variable: agenda_summary
```

## Exemple d'appel (automation vacances)

```yaml
- alias: Script Générateur Agenda Vacances
  action: script.tts_agenda_generate
  data:
    events_holidays: "{{ agenda_holidays['calendar.jours_feries_en_france']['events'] }}"
    events_school: "{{ agenda_holidays['calendar.calendrier_scolaire_zone_a']['events'] }}"
    events_family: "{{ agenda_famille['calendar.famille']['events'] }}"
  response_variable: agenda_summary
```

## Entités utilisées

- `ai_task.google_ai_task_2` (moteur IA)
- `weather.lyon` (météo intégrée dans le prompt)

## Personnalisation future

- Paramètre `lang` pour multilingue
- Paramètre `include_weather` pour supprimer le bloc météo
- Filtrage YAML préalable d'événements (durée, catégorie)

## Mode

`queued` : évite collisions si plusieurs appels simultanés.

## État

✅ Actif
