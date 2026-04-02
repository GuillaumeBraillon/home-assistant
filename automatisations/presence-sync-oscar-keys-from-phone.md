# [Presence] Sync Oscar Keys From Phone

## Description

Force les clés d'Oscar à `not_home` quand les deux sources de présence de son téléphone sont absentes :

- `device_tracker.samsung_e10` pour le GPS
- `device_tracker.a55_de_oscar` pour le Wi-Fi

## Logique

1. Déclenchement quand un des deux trackers téléphone passe à `not_home` pendant 5 minutes
2. Vérification que les deux trackers téléphone sont toujours à `not_home`
3. Vérification que `device_tracker.nut_vert_presence` n'est pas déjà à `not_home`
4. Appui sur `button.change_presence_nut_oscar`

## Entités utilisées

- `device_tracker.samsung_e10`
- `device_tracker.a55_de_oscar`
- `device_tracker.nut_vert_presence`
- `button.change_presence_nut_oscar`
