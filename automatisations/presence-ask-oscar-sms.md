# [Presence] ASK SMS Oscar

## Description

Automatisation de question interactive pour savoir si Oscar est seul à la maison quand ses clés sont actives et que les parents ne sont pas présents.

Le nom historique contient encore `SMS`, mais le mécanisme repose maintenant sur des notifications mobiles interactives.

## Déclencheurs

Changement d'état sur :

- `person.guillaume`
- `person.nelly`
- `person.oscar`

## Conditions métier

La question est posée si :

- les clés d'Oscar sont actives (`input_boolean.oscar_active == on`)
- Oscar est à la maison
- les parents ne sont pas à la maison
- le déclencheur correspond à un vrai changement d'état

## Flux

1. Envoi d'une question interactive à `guillaume`, `nelly` et `oscar`
2. Attente d'une réponse via `script.notify_ask_mobile`
3. Si timeout : notification de suivi et désactivation des clés d'Oscar
4. Si réponse `yes` : notification de suivi, sans activer l'alarme
5. Si réponse `no` : notification de suivi et désactivation des clés d'Oscar

## Réponses

- `yes` = Oscar est bien seul à la maison, on ne change rien
- `no` = Oscar n'est pas à la maison, on peut désactiver ses clés
- `timeout` = pas de réponse, on désactive les clés

## Scripts utilisés

- `script.notify_ask_mobile`
- `script.notify_send_notifications`

## Entités utilisées

- `person.guillaume`
- `person.nelly`
- `person.oscar`
- `input_boolean.oscar_active`
- `button.change_presence_nut_oscar`
