# [Notify] Ask Mobile

## Description

Script de question interactive basé sur Home Assistant Companion.

Il envoie une notification avec boutons, attend une réponse via l'événement `mobile_app_notification_action`, puis renvoie un résultat structuré au caller via `response_variable`.

## Paramètres

- `target` : une ou plusieurs cibles logiques séparées par `;`
- `ask_tag` : identifiant logique de la question
- `title` : titre de la notification
- `message` : texte de la question
- `icon` : icône Material Design
- `channel` : canal de notification
- `yes_title` : libellé du bouton positif, défaut `Oui`
- `no_title` : libellé du bouton négatif, défaut `Non`
- `timeout` : délai d'attente en secondes, défaut `300`

## Cibles supportées

- `guillaume`
- `nelly`
- `oscar`
- `parents`
- `all`

## Réponse renvoyée

Le script renvoie un objet de réponse contenant :

- `answered` : `true` ou `false`
- `answer` : `yes`, `no`, `timeout` ou `invalid_target`
- `respondent` : cible logique associée à l'action reçue
- `action` : identifiant d'action reçu
- `timeout_seconds`
- `tag`

## Notes

- Pour les cibles groupées (`parents`, `all`), `respondent` correspond à la cible logique appelée, pas à la personne physique exacte qui a appuyé.
- Le script n'écrit pas de notification persistante ni de log de message pour éviter le bruit.
- Les notifications sont effacées automatiquement après réponse ou timeout.

## Exemple

```yaml
action: script.notify_ask_mobile
data:
  target: "guillaume;nelly"
  ask_tag: ask_presence
  title: "[Présence]"
  message: "Oscar est-il seul à la maison ?"
  icon: mdi:account-question-outline
  channel: presence
  yes_title: "Oui"
  no_title: "Non"
  timeout: 300
response_variable: ask_result
```

Puis dans l'automatisation :

```yaml
{{ ask_result.answer == 'yes' }}
{{ ask_result.answer == 'no' }}
{{ ask_result.answer == 'timeout' }}
```
