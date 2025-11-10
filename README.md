# Home Assistant – Automatisations & Scripts

Ce dépôt contient des automatisations et scripts Home Assistant pour la gestion domotique avancée d’un foyer (Lyon, France).

## Structure du projet

- `automatisations/` : Automatisations YAML (rappels école, gestion présence, etc.)
- `scripts/` : Scripts réutilisables (TTS, fun facts, gestion volume, MQTT…)
- Chaque fichier YAML a une documentation `.md` associée expliquant son usage et sa logique.
- `.github/copilot-instructions.md` : Guide pour agents IA et contributeurs (conventions, patterns, workflow).

## Principaux fichiers

- `automatisations/tts-school-reminders.yaml` : Rappels TTS pour horaires d’école (début/fin, fun facts, urgences).
- `scripts/facts-random.yaml` : Génération de fun facts variés pour enfants, prêt à diffuser en TTS.
- `scripts/tts-send-message.yaml` : Diffusion intelligente de messages TTS (Google Home, MQTT/JPI, gestion volume, son SNCF).

## Bonnes pratiques

- Utilisation du templating Jinja2 pour la logique, les triggers et la personnalisation des messages.
- Documentation systématique dans les fichiers `.md`.
- Nommage en kebab-case avec préfixe fonctionnel (`tts-`, `facts-`, etc.).
- Respect de l’architecture Home Assistant (entités, groupes, helpers).

## Exemples d’usage

```yaml
# Diffuser un fun fact à Eliott
- action: script.facts_random
  response_variable: fact_result
- action: script.tts_send_message
  data:
    media_player: media_player.ni_oscar_ni_parents
    message: "Il est {{ time }}. {{ fact_result.fun_fact }}"
    sncf: false
```

## Pour contribuer

- Lire `.github/copilot-instructions.md` pour les conventions et le workflow.
- Documenter chaque ajout ou modification dans le `.md` associé.
- Respecter la logique et la lisibilité YAML.

## Localisation

- Langue : français
- Fuseau horaire : Europe/Paris
- Contexte : Lyon, France

---

Pour toute question ou suggestion, voir les docs `.md` ou contacter le propriétaire du dépôt.
