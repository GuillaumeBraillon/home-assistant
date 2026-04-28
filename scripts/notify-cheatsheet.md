# Cheatsheet notifications mobiles

## Choix rapide

1. Notification normale : `preset: default`
2. Notification urgente : `preset: alarm`
3. Notification urgente avec lecture vocale mobile : `preset: alarm_tts`
4. Question avec boutons : utiliser `script.notify_ask_mobile`

## Quel `channel` choisir ?

| Besoin | Channel recommande | Preset recommande |
| --- | --- | --- |
| Presence, arrivee, depart | `presence` | `default` |
| Question de presence | `presence` | `alarm_tts` |
| Sante, poids, mesures | `health` | `default` |
| SMS converti en notif | `sms` | `default` |
| Alerte forte, simulation d'appel, alarme | `alarm_stream` | `alarm` ou `alarm_tts` |
| Alerte technique | `Alert` | `default` ou `alarm` |
| Evenement JPI | `JPI` | `default` |
| Question interactive hors presence | `questions` ou channel metier | `alarm` |

## Quand reutiliser un channel existant ?

- Reutiliser si la notif appartient a une famille deja connue : `presence`, `health`, `sms`, `Alert`, `JPI`
- Utiliser `alarm_stream` si la notif est urgente et ne rentre dans aucune famille claire
- Creer un nouveau `channel` seulement si tu veux un regroupement ou un comportement Android distinct

## Quel script utiliser ?

### Envoi simple

Utiliser `script.notify_send_notifications`

```yaml
action: script.notify_send_notifications
data:
  target: guillaume
  title: "[Presence]"
  message: "Oscar est arrive."
  icon: mdi:map-marker
  channel: presence
  preset: default
```

### Alerte urgente

Utiliser `script.notify_send_notifications`

```yaml
action: script.notify_send_notifications
data:
  target: guillaume;nelly
  title: "[Alarme]"
  message: "La porte d'entree est ouverte."
  icon: mdi:door-open
  channel: alarm_stream
  preset: alarm
```

### Alerte urgente avec voix sur le telephone

Utiliser `script.notify_send_notifications`

```yaml
action: script.notify_send_notifications
data:
  target: guillaume
  title: "[Alarme]"
  message: "La porte d'entree est ouverte."
  icon: mdi:door-open
  channel: alarm_stream
  preset: alarm_tts
  mobile_tts_text: "Attention. La porte d'entree est ouverte."
```

### Question avec reponse Oui / Non

Utiliser `script.notify_ask_mobile`

```yaml
action: script.notify_ask_mobile
data:
  target: guillaume;nelly
  ask_tag: ask_presence
  title: "[Presence]"
  message: "Oscar est-il seul a la maison ?"
  icon: mdi:account-question-outline
  channel: presence
  preset: alarm_tts
  yes_title: "Oui"
  no_title: "Non"
  timeout: 300
response_variable: ask_result
```

## Regles utiles

- `default` = notification standard
- `alarm` = visibilite maximale, sans TTS mobile
- `alarm_tts` = visibilite maximale + lecture vocale mobile
- `mobile_tts_text` n'est utile qu'avec `preset: alarm_tts`
- pour une question interactive, preferer `script.notify_ask_mobile`
- si tu hesites entre deux channels, privilegie le channel metier deja existant
