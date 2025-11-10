# [Facts] Fun Fact Aléatoire

## Description

Script Home Assistant qui retourne une phrase fun fact aléatoire, adaptée pour diffusion TTS à destination d'un enfant.

## Fonctionnement

- Sélectionne une phrase complète et naturelle dans une liste prédéfinie.
- Retourne la phrase via la variable `fact_result.fun_fact`.
- Les formulations sont fixes et adaptées à l'oral.

## Entrées

Aucune (appel simple).

## Sortie

- `fact_result.fun_fact` : phrase fun fact prête à être diffusée.

## Exemples de phrases

- "Eliott, une voiture de Formule 1 colle à la piste grâce à son aileron."
- "Eliott, la lumière du Soleil met environ 8 minutes pour venir jusqu'à nous."
- ...

## Usage typique

Appel depuis une automatisation :

```yaml
action: script.facts_random
  response_variable: fact_result
```

Puis utilisation :

```yaml
message: "{{ fact_result.fun_fact }}"
```
