# [Vacuum] Ask Start Alarm On

## Description

Lorsque le mode maison passe à `Alarme`, que l'aspirateur est sur son dock et que plus personne n'est à la maison, cette automatisation demande s'il faut annuler le lancement de l'aspirateur.

## Logique

1. Déclenchement quand `input_select.modes_maison` passe à `Alarme`
2. Vérification que `vacuum.dreamebot_d10_plus` est `docked`
3. Vérification que `group.persons` est `not_home`
4. Détermination de la dernière personne passée en `not_home`
5. Envoi de la question à `guillaume` et à cette dernière personne si elle est différente
6. Attente d'une réponse via `script.notify_ask_mobile`
7. Si réponse `yes` : ne pas lancer l'aspirateur
8. Si réponse `no` : lancer l'aspirateur
9. Si timeout : lancer l'aspirateur

## Ciblage

- toujours `guillaume`
- plus la dernière personne partie si différente

## Réponses

Dans cette automatisation :

- `yes` = annuler le lancement
- `no` = lancer l'aspirateur

## Scripts utilisés

- `script.notify_ask_mobile`
- `script.notify_send_notifications`

## Entités utilisées

- `input_select.modes_maison`
- `vacuum.dreamebot_d10_plus`
- `group.persons`
