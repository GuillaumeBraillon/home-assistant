# [TTS] Send TTS

## Description

Script réutilisable pour envoyer des messages TTS (Text-to-Speech) sur les enceintes Google Home avec gestion intelligente de l'état des media players et options avancées.

## Paramètres

- `media_player` : ID de l'enceinte cible (ex: `media_player.ni_oscar_ni_parents`)
- `message` : Message texte à diffuser
- `sncf` : Booléen (optionnel) pour jouer le son SNCF avant le message

## Logique du script

### 1. Gestion du Nest Hub

Si le Nest Hub affiche Home Assistant Lovelace, il est éteint pour éviter les conflits.

### 2. Gestion des media players du séjour

**Si le media_player cible est dans le groupe séjour** (`mi_smart_home_salon`, `nest_hub`, `sejour`, etc.) :

- **ET qu'un media player est en cours de lecture** :
  - Envoie le TTS via MQTT avec une pause de 10 secondes
- **Sinon** :
  - Configure le volume avec `input_number.now_volume`
  - Joue optionnellement le son SNCF (5 secondes)
  - Utilise `tts.speak` avec `tts.google_cloud`
  - Attend 10 secondes
  - Attend que le media player arrête de jouer (timeout 1 minute)
  - Éteint le media player

### 3. Notifications

- Écrit les notifications via `script.notify_write_notifications`
- Option (désactivée) : notification persistante avec titre formaté

## Media players du séjour

- `media_player.mi_smart_home_salon`
- `media_player.nest_hub`
- `media_player.sejour`
- `media_player.tout_sauf_enfants`
- `media_player.tout_sauf_parents`
- `media_player.toutes_les_enceintes`
- `media_player.ni_oscar_ni_parents`

## Mode d'exécution

`queued` : Les appels sont mis en file d'attente et exécutés séquentiellement

## Services utilisés

- `media_player.turn_off` : Éteindre les enceintes
- `media_player.volume_set` : Définir le volume
- `media_player.play_media` : Jouer le son SNCF
- `tts.speak` : Service TTS principal
- `script.mqtt_send_topics` : Envoi MQTT pour TTS avec pause
- `script.notify_write_notifications` : Écriture des notifications

## Fichiers média

- `media-source://media_source/media/SNCF 2015.wav` : Son de notification SNCF

## Entités référencées

- `input_number.now_volume` : Volume actuel pour les TTS (0-100)
- `tts.google_cloud` : Service Google Cloud TTS
