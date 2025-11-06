# Instructions pour agents IA - Home Assistant

## Vue d'ensemble du projet

Ce dépôt contient les **automatisations et scripts Home Assistant** (fichiers YAML) pour gérer des scénarios domotiques personnalisés. Les automatisations sont stockées dans `automatisations/` et les scripts dans `scripts/`. Tous utilisent le moteur de templating Jinja2 de Home Assistant.

## Architecture et conventions

### Structure des fichiers

- **`automatisations/`** : Fichiers YAML d'automatisations Home Assistant
- **`scripts/`** : Scripts réutilisables appelés par les automatisations
- Chaque fichier représente une automatisation complète avec alias, déclencheurs, conditions et actions
- **Convention de nommage** : `kebab-case` avec préfixe catégorie (ex: `tts-school-reminders.yaml`, `tts-send-message.yaml`)
  - Pas d'espaces ni de caractères spéciaux
  - Format : `categorie-description-fonctionnelle.yaml`
  - Catégories existantes : `tts-` (Text-to-Speech), d'autres catégories seront ajoutées selon les besoins
- **Documentation** : Chaque fichier YAML a un fichier `.md` au même niveau avec le même nom pour la documentation détaillée

### Structure YAML des automatisations

Chaque automatisation suit cette structure obligatoire :

```yaml
alias: "[Catégorie] Nom descriptif"
description: ""
triggers:
  - trigger: template
    value_template: "{{ condition jinja2 }}"
    alias: Description du déclencheur
    id: identifiant_unique
conditions:
  - condition: and/or/state
    # Conditions pour exécution
actions:
  - variables:
      var_name: "{{ valeur }}"
  - action: service.name
    data:
      param: "{{ variable }}"
mode: single|parallel|queued|restart
```

## Patterns spécifiques du projet

### Templating Jinja2 pour déclencheurs temporels

Les déclencheurs utilisent des comparaisons de temps précises avec `value_template`:

```yaml
value_template: >-
  {{ (now().timestamp() | timestamp_custom('%H:%M')) ==
  (today_at(states('input_datetime.entity')) - timedelta(minutes=5)).strftime('%H:%M') }}
```

**Bonnes pratiques observées** :

- Utiliser `today_at()` pour créer des timestamps du jour courant
- `timedelta()` pour calculer des décalages temporels
- `strptime()` pour parser les durées depuis les entités `input_datetime`
- `timestamp_custom('%H:%M')` pour formater l'heure au format comparable

### Variables et actions

Les actions définissent toujours des `variables` avant l'exécution :

```yaml
actions:
  - variables:
      time: "{{ now().strftime('%H:%M') }}"
      message: |-
        {% if trigger.id == 'condition' %}
          {{ ["Message 1", "Message 2"] | random }}
        {% endif %}
```

### Messages TTS randomisés

Pattern pour créer des messages Text-to-Speech variés :

- Utiliser des listes de phrases avec `| random` pour éviter la répétition
- Structurer avec `{% if trigger.id == 'id' %}` pour contextualiser les messages selon le déclencheur

### Entités référencées

Les automatisations et scripts s'appuient sur ces types d'entités Home Assistant :

- **`input_datetime.*`** : Horaires configurables
- **`input_boolean.*`** : Interrupteurs logiques
- **`input_number.*`** : Valeurs numériques (ex: volume TTS)
- **`group.*`** : Groupes d'entités
- **`sensor.*`** : Capteurs météo/états
- **`media_player.*`** : Sorties audio TTS
- **`tts.*`** : Services Text-to-Speech

### Actions et scripts

Les actions appellent des services ou scripts Home Assistant :

```yaml
action: script.tts_send_tts
data:
  media_player: media_player.ni_oscar_ni_parents
  message: "{{ message }}"
  sncf: "{{ sncf }}"
```

### Structure des scripts

Les scripts suivent une structure avec `sequence` et `mode` :

```yaml
alias: "[Catégorie] Nom du script"
sequence:
  - if:
      - condition: template
        value_template: "{{ condition }}"
    then:
      - action: service.name
        data:
          param: "{{ variable }}"
mode: queued|single|parallel|restart
icon: mdi:icon-name
```

**Patterns observés dans les scripts** :

- **Logique conditionnelle** : Utiliser `if/then/else` pour gérer différents cas d'usage
- **Vérifications d'état** : Tester l'état des `media_player` avant d'agir
- **Gestion du volume** : Ajuster `volume_level` avec des entités `input_number`
- **Délais et attentes** : Utiliser `delay` et `wait_template` pour synchroniser les actions
- **Mode `queued`** : Permet l'exécution séquentielle de plusieurs appels simultanés

## Workflow de développement

### Création d'une nouvelle automatisation

1. Créer un fichier YAML dans `automatisations/` avec la convention kebab-case : `categorie-nom-descriptif.yaml`
2. Créer un fichier `.md` correspondant pour la documentation détaillée (même nom)
3. Définir un `alias` descriptif unique (peut utiliser caractères spéciaux et espaces)
4. Structurer les `triggers` avec des IDs uniques pour identification dans les actions
5. Ajouter des `conditions` pour limiter l'exécution (état des entités, présence, etc.)
6. Construire les `actions` avec variables puis appels de services
7. Choisir le `mode` approprié (`single` par défaut pour éviter les exécutions multiples)
8. Documenter dans le fichier `.md` : description, déclencheurs, conditions, actions, entités référencées

### Validation

- Respecter l'indentation YAML stricte (2 espaces)
- Valider la syntaxe Jinja2 dans les templates
- Tester les `trigger.id` utilisés dans les conditions de messages
- Vérifier que toutes les entités référencées existent dans Home Assistant

### Environnement

- **Pas d'environnement de test** : Les automatisations sont déployées directement en production
- Tester soigneusement la syntaxe YAML et les templates avant déploiement
- Vérifier la logique des conditions pour éviter les déclenchements non désirés

## Langue du projet

Le projet est en **français** :

- Messages TTS, commentaires et descriptions en français
- Noms d'entités en anglais (convention Home Assistant)
- Alias d'automatisations en français avec préfixe anglais pour catégorie

## Configuration géographique

- **Localisation** : Lyon, France
- Les capteurs météo et horaires doivent être configurés pour le fuseau horaire Europe/Paris
- Entités météo référencées : `sensor.lyon_temperature`, `sensor.lyon_daily_original_condition`
