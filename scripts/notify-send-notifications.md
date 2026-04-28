# [Notify] Send notifications To Mobiles

## Description

Script générique d'envoi de notifications mobiles via les services `notify.*` déclarés dans `configuration.yaml`.

Il centralise :

- le mapping logique des cibles (`guillaume`, `nelly`, `oscar`, `parents`, `all`)
- l'envoi vers une ou plusieurs cibles séparées par `;`
- l'injection de données mobiles avancées (`actions`, `tag`, `timeout`, `priority`, etc.)
- l'effacement d'une notification via `clear_notification`

## Paramètres

- `target` : cible logique simple ou multiple, ex. `guillaume` ou `guillaume;nelly`
- `title` : titre de la notification
- `message` : message de la notification
- `icon` : icône Material Design
- `channel` : canal Android / logique de regroupement
- `preset` : comportement de priorité, `default`, `alarm` ou `alarm_tts`
- `mobile_tts_text` : texte lu par le téléphone si `preset: alarm_tts`
- `mobile_data` : dictionnaire optionnel fusionné dans `data`
- `clear` : booléen optionnel pour effacer une notification
- `clear_tag` : tag de notification à effacer
- `persistent_notification` : booléen optionnel, défaut `true`
- `write_log` : booléen optionnel, défaut `true`

## Cibles supportées

- `guillaume` -> `notify.guillaume_device`
- `nelly` -> `notify.nelly_device`
- `oscar` -> `notify.oscar_device`
- `parents` -> `notify.parents_devices`
- `all` -> `notify.all_devices`

## Comportement

### Envoi standard

Le script envoie la notification à toutes les cibles résolues, puis peut :

- créer une notification persistante interne Home Assistant
- écrire le message via `script.notify_write_notifications`

Ces deux comportements sont désactivables.

Par defaut, une notification standard envoie :

- `importance: high`
- `sticky: true`
- `persistent: true`
- `car_ui: true`

### Effacement

Si `clear: true`, le script envoie `clear_notification` à chaque cible résolue avec `clear_tag`.

### Cible invalide

Si `target` ne mappe sur aucune cible connue, le script ne tente aucun envoi mobile et crée une notification persistante d'erreur.

### Presets

#### `preset: default`

Preset par defaut si rien n'est precise.

Utilisation :

- notification informative
- rappel simple
- changement d'etat non critique

Effet :

- garde l'importance standard `high`
- ne declenche pas de lecture TTS sur le mobile

#### `preset: alarm`

Utilisation :

- evenement urgent
- action immediate attendue
- alerte qui doit rester tres visible

Effet :

- `ttl: 0`
- `priority: high`
- `importance: max`
- `live_update: true`
- `critical_text: [ALARME]`

#### `preset: alarm_tts`

Utilisation :

- meme cas que `alarm`
- avec lecture vocale sur le telephone

Effet :

- applique les memes priorites que `alarm`
- envoie d'abord un message `TTS` sur le mobile avec `mobile_tts_text`
- attend 10 secondes
- envoie ensuite la notification normale

## Choisir `channel` et `preset`

### Regle rapide

1. Si la notification doit parler sur le telephone, choisir `preset: alarm_tts`.
2. Si la notification est urgente mais sans TTS, choisir `preset: alarm`.
3. Sinon, garder `preset: default`.
4. Choisir ensuite le `channel` selon le type metier de la notification.

### Channels utilises dans ce repo

| Channel | Quand l'utiliser | Preset recommande | TTS mobile | Exemple |
| --- | --- | --- | --- | --- |
| `presence` | Arrivee, depart, verification de presence, question sur qui est a la maison | `default` ou `alarm_tts` si question urgente | Optionnel | Oscar est-il seul a la maison ? |
| `health` | Poids, sante, mesure personnelle | `default` | Non | Nouvelle pesee |
| `sms` | Message converti depuis un ancien flux SMS | `default` | Non | SMS recu via MQTT |
| `alarm_stream` | Alerte forte, simulation d'appel, besoin d'attention immediate | `alarm` ou `alarm_tts` | Souvent oui | `makeCall`, alarme, demande critique |
| `Alert` | Alerte technique, equipement offline, etat anormal | `default` ou `alarm` selon gravite | Rare | Capteur inactif, service offline |
| `JPI` | Notifications techniques liees au telephone JPI | `default` | Non | Smartphone JPI offline |
| `questions` | Notification interactive si tu n'utilises pas `script.notify_ask_mobile` | `alarm` en general | Optionnel | Oui / Non manuel |

### Recommandation pratique

| Besoin | Channel | Preset |
| --- | --- | --- |
| Information simple | channel metier dedie | `default` |
| Evenement important mais non critique | channel metier dedie | `default` |
| Alerte urgente | `alarm_stream` ou channel metier deja etabli | `alarm` |
| Alerte urgente avec lecture vocale mobile | `alarm_stream` | `alarm_tts` |
| Question interactive | `presence` / `alarm_stream` / `questions` selon contexte | `alarm` ou `alarm_tts` |
| Notification technique | `Alert` ou `JPI` | `default` |

## Guide de choix

### Utiliser `script.notify_ask_mobile` ou `script.notify_send_notifications` ?

- si tu veux poser une question avec boutons et attendre une reponse, utilise `script.notify_ask_mobile`
- si tu veux seulement envoyer une notification, utilise `script.notify_send_notifications`

`script.notify_ask_mobile` s'appuie deja sur `script.notify_send_notifications` et gere :

- les actions `Oui` / `Non`
- le timeout
- l'effacement de la notification apres reponse

### Quel `channel` choisir pour une nouvelle automation ?

- reutilise un `channel` existant si la nouvelle notification appartient clairement a une famille deja en place
- cree un nouveau `channel` seulement si tu veux separer le comportement Android, le regroupement visuel ou les reglages sonores
- pour une notification urgente sans famille metier claire, prends `alarm_stream`
- pour une notification informative sans famille metier claire, prends un nom metier explicite, par exemple `volets`, `chauffage` ou `courses`

### Quand creer un nouveau preset ?

Pour l'instant, inutile. Les trois cas couvrent deja le besoin :

- `default` : normal
- `alarm` : urgent
- `alarm_tts` : urgent avec voix

## Exemples

### Envoi simple

```yaml
action: script.notify_send_notifications
data:
  target: guillaume
  title: "[Présence]"
  message: "Oscar est arrivé."
  icon: mdi:map-marker
  channel: presence
```

### Envoi multi-cibles

```yaml
action: script.notify_send_notifications
data:
  target: "guillaume;nelly"
  title: "[Aspirateur]"
  message: "Je lance l'aspirateur."
  icon: mdi:robot-vacuum
  channel: alarm_stream
  preset: alarm
```

### Notification interactive

```yaml
action: script.notify_send_notifications
data:
  target: guillaume
  title: "[Question]"
  message: "Tu confirmes ?"
  icon: mdi:message-question
  channel: questions
  persistent_notification: false
  write_log: false
  mobile_data:
    tag: ask_example
    actions:
      - action: YES
        title: "Oui"
      - action: NO
        title: "Non"
```

### Notification urgente avec TTS mobile

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

### Effacement

```yaml
action: script.notify_send_notifications
data:
  target: guillaume
  clear: true
  clear_tag: ask_example
```
