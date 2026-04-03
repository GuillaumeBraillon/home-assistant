# [Presence] ASK SMS Oscar

## Description

Automatisation de question interactive pour savoir si Oscar est seul à la maison quand son statut de présence est actif et que les parents ne sont pas présents.

Le nom historique contient encore `SMS`, mais le mécanisme repose maintenant sur des notifications mobiles interactives.

## Déclencheurs

Changement d'état sur :

- `person.guillaume`
- `person.nelly`
- `person.oscar`

## Conditions métier

La question est posée si :

- le statut d'Oscar est actif (`input_boolean.oscar_active == on`)
- Oscar est à la maison
- les parents ne sont pas à la maison
- et qu'un événement utile se produit :
- retour d'Oscar à `home`
- ou départ de `home` d'un des parents

## Flux

1. Envoi d'une question interactive à `guillaume`, `nelly` et `oscar`
2. Attente d'une réponse via `script.notify_ask_mobile`
3. Si timeout : notification de suivi et désactivation du statut d'Oscar
4. Si réponse `yes` : notification de suivi, sans activer l'alarme
5. Si réponse `no` : notification de suivi et désactivation du statut d'Oscar
6. Si Oscar revient à `home` alors que son statut est inactif : réactivation de `input_boolean.oscar_active`

## Réponses

- `yes` = Oscar est bien seul à la maison, on ne change rien
- `no` = Oscar n'est pas à la maison, on peut désactiver son statut
- `timeout` = pas de réponse, on désactive son statut

## Scripts utilisés

- `script.notify_ask_mobile`
- `script.notify_send_notifications`

## Entités utilisées

- `person.guillaume`
- `person.nelly`
- `person.oscar`
- `input_boolean.oscar_active`
