# recette-qa

Plugin Claude Code pour le workflow de recette QA : rédaction du cahier de recette après une feature/fix, exécution des tests dans un vrai navigateur (Playwright), tests de non-régression (TNR) sur les parcours critiques, et rapport de test avec captures d'écran.

## Installation

Dans un projet cible, depuis Claude Code :

```
/plugin marketplace add /home/lionel/projects/recette-qa
/plugin install recette-qa
```

## Workflow

1. **`/recette-init`** (une fois par projet) — crée l'arborescence `recette/`, installe Playwright si absent, met en place le fichier de config secret et le bloque au modèle.
2. **`/recette-cahier <thème> <feature/fix>`** — l'agent `recette-redacteur` écrit le cahier de recette (cas de test) et les specs Playwright correspondantes, cas critiques marqués `@critical`, cas de non-régression marqués `@tnr`.
3. **`/recette-test [thème|critique|tnr|tout]`** — l'agent `recette-testeur` exécute les tests dans un navigateur (Playwright), génère un run horodaté.
4. **`/recette-rapport [run-id]`** — l'agent `recette-rapporteur` construit la synthèse Go/No-Go et le rapport par thème avec captures d'écran.

## Arborescence générée dans le projet cible

```
recette/
  .env.recette          # secret : URL + identifiants — jamais lu par le modèle, jamais commité
  .env.recette.example
  .auth/                # état de connexion Playwright (généré)
  recette.settings.json # réglages non-secrets (qualité screenshot, headless, ...)
  cahiers/
    <theme>/<slug>.md   # cahiers de recette, un fichier par feature/fix
  tests/
    helpers/capture.ts
    <theme>/<slug>.spec.ts
  reports/
    <run-id>/
      summary.md
      <theme>/
        report.md
        screenshots/
```

## Sécurité du fichier de config

`recette/.env.recette` contient l'URL de la recette et les identifiants. Il est :
- exclu du modèle par une règle `deny` ajoutée à `.claude/settings.json` (blocage réel, pas seulement une convention) ;
- exclu de git via `.gitignore` ;
- lu uniquement par `playwright.config.ts` (via `dotenv`), jamais par un agent Claude.

## Critique vs TNR

- `@critical` : le cas bloque le déploiement de la feature/fix s'il échoue.
- `@tnr` : le cas est rejoué à chaque suite de non-régression, même quand ce n'est pas sa feature qui a changé (parcours transverses : login, paiement, etc.).

## Maîtrise du poids disque

Les captures sont prises uniquement aux points utiles (pas à chaque action), compressées en jpeg qualité réduite par le helper `captureStep`, et rangées par run/thème/cas de test. Aucune purge automatique n'est faite par le plugin — à gérer manuellement si `recette/reports/` grossit trop.
