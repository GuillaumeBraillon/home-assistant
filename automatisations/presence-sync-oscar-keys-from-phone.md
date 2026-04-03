# [Presence] Sync Oscar Keys From Phone

## Description

Désactive `input_boolean.oscar_active` quand le téléphone d'Oscar est absent sur toutes les sources retenues :

- `device_tracker.samsung_a55` pour le GPS
- `device_tracker.a55_de_oscar` pour le Wi-Fi Freebox
- `device_tracker.nmap_tracker_1a_d6_c6_43_dc_ff` pour le Wi-Fi Nmap

## Logique

1. Déclenchement quand un des trackers téléphone passe à `not_home` pendant 5 minutes
2. Vérification que le GPS est toujours à `not_home`
3. Vérification que les deux trackers Wi-Fi sont toujours à `not_home`
4. Désactivation de `input_boolean.oscar_active`

## Entités utilisées

- `device_tracker.samsung_a55`
- `device_tracker.a55_de_oscar`
- `device_tracker.nmap_tracker_1a_d6_c6_43_dc_ff`
- `input_boolean.oscar_active`
