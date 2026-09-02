---
name: recette-test
description: Exécute les tests Playwright de recette dans un navigateur — une feature/fix, un thème, les cas critiques, ou toute la suite de non-régression (TNR) avant déploiement. À lancer après recette-cahier.
---

Détermine le périmètre demandé par l'utilisateur : feature/fix précise, thème, « critiques », « TNR »/« avant déploiement », ou « tout ». Par défaut si rien n'est précisé et qu'un déploiement est mentionné : périmètre TNR (`@critical` + `@tnr` sur toute la suite, pas seulement le thème modifié).

Vérifie que `recette/.env.recette.example` existe (signe que `recette-init` est passé) ; si `recette/` est absent, renvoie vers `recette-init` et arrête-toi. Ne vérifie et ne mentionne jamais le contenu du fichier secret, seulement son existence si besoin — c'est de toute façon le rôle de l'agent testeur.

Délègue l'exécution à l'agent `recette-qa:recette-testeur` (Agent tool, `subagent_type: "recette-qa:recette-testeur"`), en lui donnant le périmètre déterminé.

Relaie le résumé de l'agent à l'utilisateur : run id, résultats, et surtout si un test `@critical` a échoué (bloquant). Propose d'enchaîner sur `recette-rapport` si l'utilisateur veut un rapport avec captures.
