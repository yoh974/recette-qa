---
name: recette-cahier
description: Rédige le cahier de recette QA et les specs Playwright pour une fonctionnalité ou un fix qui vient d'être développé. À lancer après le développement, avant les tests. Args attendus — thème, nom court de la feature/fix, description.
---

Récupère du message de l'utilisateur (ou pose la question si manquant) :
- le **thème** (ex. `authentification`, `panier`) — un dossier existant sous `recette/cahiers/` si possible, sinon un nouveau ;
- un **nom court** (slug) pour la feature/fix ;
- une **description** de ce qui a été développé (ce qui a changé, périmètre) ;
- si l'information est disponible : quels parcours sont critiques (bloquants au déploiement) et lesquels doivent entrer dans la suite de non-régression (TNR).

Si `recette/` n'existe pas encore dans le projet, dis à l'utilisateur de lancer `recette-init` d'abord et arrête-toi.

Délègue ensuite la rédaction à l'agent `recette-qa:recette-redacteur` (via l'outil Agent, `subagent_type: "recette-qa:recette-redacteur"`) en lui transmettant thème, slug, description, et tout ce que tu sais sur la criticité. Laisse l'agent poser lui-même les questions de criticité restantes s'il en a besoin.

Une fois l'agent terminé, relaie à l'utilisateur son résumé (fichiers créés, nombre de cas, combien sont `@critical`/`@tnr`) sans le reformuler en détail.
