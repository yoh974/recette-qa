---
name: recette-rapport
description: Génère le rapport de recette (synthèse Go/No-Go + rapport par thème avec captures d'écran) à partir d'un run déjà exécuté par recette-test. Args optionnel — identifiant de run (par défaut, le plus récent).
---

Récupère l'identifiant de run si l'utilisateur en a donné un ; sinon laisse l'agent prendre le plus récent sous `recette/reports/`.

Délègue à l'agent `recette-qa:recette-rapporteur` (Agent tool, `subagent_type: "recette-qa:recette-rapporteur"`).

Relaie à l'utilisateur uniquement le verdict Go/No-Go, les échecs critiques s'il y en a, et le chemin du `summary.md`. N'affiche pas les images ni le détail des rapports par thème — l'utilisateur les ouvre lui-même si besoin.
