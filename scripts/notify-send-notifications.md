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

### Effacement

Si `clear: true`, le script envoie `clear_notification` à chaque cible résolue avec `clear_tag`.

### Cible invalide

Si `target` ne mappe sur aucune cible connue, le script ne tente aucun envoi mobile et crée une notification persistante d'erreur.

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

### Effacement

```yaml
action: script.notify_send_notifications
data:
  target: guillaume
  clear: true
  clear_tag: ask_example
```
