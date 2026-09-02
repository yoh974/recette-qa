---
name: recette-init
description: Initialise le workflow de recette QA dans le projet courant — arborescence recette/, Playwright (installé si absent), fichier de config secret (.env.recette) exclu du modèle et de git, réglages non-secrets. À lancer une fois par projet avant d'utiliser recette-cahier/recette-test/recette-rapport.
---

Tu initialises le workflow de recette QA dans le projet **courant** (celui où l'utilisateur travaille, pas ce plugin). Exécute chaque étape toi-même avec Bash/Write/Edit ; ne délègue pas à un agent, c'est du scaffolding déterministe.

## 1. Playwright

Vérifie `npx playwright --version`. S'il échoue :
```bash
npm init playwright@latest -- --quiet --browser=chromium --no-examples
```
Si Playwright est déjà présent, ne touche pas à sa config existante en dehors de ce que cette init ajoute.

## 2. Arborescence recette/

Crée si absent :
```
recette/
  cahiers/            # cahiers de recette par thème (recette-cahier)
  tests/
    helpers/
  reports/            # runs de test (recette-test, recette-rapport)
  .auth/              # état de connexion Playwright (généré, jamais commité)
```

Copie les templates du plugin (chemin `${CLAUDE_PLUGIN_ROOT}/templates/`) vers le projet, sans écraser un fichier déjà personnalisé par l'utilisateur :

- `templates/playwright.config.ts.tmpl` → `playwright.config.ts` (racine projet) — **seulement si absent**. S'il existe déjà, n'écrase pas : signale à l'utilisateur les réglages à reprendre manuellement (testDir `./recette/tests`, globalSetup, reporter json vers `recette/reports/.last-run.json`).
- `templates/global-setup.ts.tmpl` → `recette/global-setup.ts`
- `templates/helpers/capture.ts.tmpl` → `recette/tests/helpers/capture.ts`
- `templates/env.recette.example` → `recette/.env.recette.example`

## 3. Fichier secret — jamais touché par le modèle

Ne crée PAS `recette/.env.recette` toi-même (tu ne dois jamais écrire ni lire de vrais identifiants). Dis simplement à l'utilisateur :

> Copie `recette/.env.recette.example` en `recette/.env.recette` et renseigne `RECETTE_BASE_URL`, `RECETTE_USER`, `RECETTE_PASSWORD`. Ce fichier restera inaccessible au modèle.

## 4. Blocage d'accès au fichier secret

Fusionne le contenu de `templates/claude-settings.snippet.json` dans `.claude/settings.json` du projet (clé `permissions.deny`, en ajoutant aux règles existantes sans les écraser ; crée le fichier s'il n'existe pas). C'est cette règle qui rend `recette/.env.recette` réellement inaccessible au modèle, pas seulement une convention.

## 5. .gitignore

Ajoute le contenu de `templates/gitignore.snippet` au `.gitignore` du projet (à la fin, s'il n'y est pas déjà).

## 6. Réglages non-secrets

Crée `recette/recette.settings.json` (celui-ci PEUT être lu par le modèle, il ne contient aucun secret) :
```json
{
  "headless": true,
  "screenshotQuality": 55,
  "themesCritiquesTouteSuite": true
}
```

## 7. Résumé final

Liste à l'utilisateur : fichiers/dossiers créés, si Playwright a été installé, et rappelle l'action manuelle restante (remplir `recette/.env.recette`). Ne continue pas vers la rédaction de cahier tant que ce fichier secret n'existe pas si l'utilisateur veut enchaîner sur `recette-test`.
