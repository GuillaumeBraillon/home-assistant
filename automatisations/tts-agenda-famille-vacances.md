# [TTS] Agenda Famille Vacances (Séjour)

## Description

Automatisation qui annonce l'agenda des prochaines 24h en période de séjour/journée :

- Événements du calendrier famille
- Jours fériés France
- Calendrier scolaire (Zone A)

Elle s'appuie sur le script IA (`script.tts_agenda_generateur_ia`) pour construire le prompt et obtenir `agenda_summary` puis crée une notification et lit le texte via TTS.

## Déclencheur

- Changement de `input_select.modes_maison` de `Matin` vers `Journée` (après 30s de stabilisation)

## Préconditions internes

- Vérification disponibilité des calendriers (pas `unavailable` / `unknown`).

## Flux des actions

1. Récupération événements famille (24h)
2. Récupération jours fériés + scolaire (durée étendue pour garder contexte, filtré ensuite par IA)
3. Appel du script IA avec: `events_holidays`, `events_school`, `events_family`
4. Réception `agenda_summary.data` (résumé format vocal naturel)
5. Notification persistante
6. Lecture TTS sur `media_player.ni_oscar_ni_parents` via `script.tts_send_tts`

## Paramètres script IA

Le script construit un prompt incluant:

- Date du jour + météo `weather.lyon`
- Listes d'événements injectées
- Règles de style (phrases naturelles, pas de listes)
- Formulations discrètes pour traitements (Metoject, Humira) si présents

## Entités référencées

- `calendar.famille`
- `calendar.calendrier_scolaire_zone_a`
- `calendar.jours_feries_en_france`
- `script.tts_agenda_generateur_ia`
- `script.tts_send_tts`
- `media_player.ni_oscar_ni_parents`
- `weather.lyon`

## Sortie

`agenda_summary.data` (texte prêt pour synthèse vocale).

## Exemple de message

> Aujourd'hui ciel clair à Lyon. Ce matin école fermée pour le jour férié, plus tard réunion familiale prévue en fin d'après-midi. Et aussi, journée de congé scolaire prolongée demain.

## Maintenance

- Vérifier présence des calendriers
- Ajuster plage horaire si besoin (24h)
- Modifier prompt dans le script global en cas de changement de style

## État

✅ Actif
