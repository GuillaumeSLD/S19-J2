# Déploiement

## Introduction

Avant de déployer une application React, on doit fournir du code compréhensible
par les navigateurs : du code transpilé (JSX, ES2015+ → ES5, SCSS → CSS…).

C'est ce code qu'il faut installer sur le serveur de production.

Pour faire ça, on utilise la commande `build` de Vite qui va générer un dossier
`dist/` prêt à l'emploi.

> **NOTE** il sera peut-être nécessaire d'autoriser le déploiement
> à partir des branches spécifiques :
>
> `Settings > Environments > Deployment branches and tags`

## Déploiement sur GitHub Pages

```yml
### @file ./.github/workflows/github-pages.yml
name: Deploy to GitHub Pages

# Contrôle l'exécution du workflow
on:
  # Déclenchement lors d'un push mais uniquement sur la branche `S19E02-gh-pages`
  push:
    branches: [ "S19E02-gh-pages" ]
  
  # Autorisation de déclencher manuellement du workflow depuis l'onglet « Actions » du dépôt GH
  workflow_dispatch:

# Définition des permissions nécessaire pour GH Pages
permissions:
  # Permission de lire le contenu du dépôt
  # (nécessaire pour le processus de checkout du code, comme dans l'étape actions/checkout@v4)
  contents: read
  # Permission d'écrire sur GH Pages
  # (essentiel pour que le processus de déploiement fonctionne correctement)
  pages: write
  # Permet de créer un token
  # (peut être nécessaire pour interagir avec d'autres services ou APIs externes pour prouver l'identité du workflow ; ex. : Google Cloud, authentification OAuth…)
  id-token: write

# Un workflow est composé d'une ou plusieurs tâches exécutées de manière séquentielle ou en parallèle
jobs:
  # Tâche pour le build du projet
  build:
    # Type du runner : la tâche s'exécute sur Ubuntu
    runs-on: ubuntu-latest
    # Étapes de la tâche
    steps:
      # Récupère le code du dépôt (copie notre code dans le runner Ubuntu → git checkout S19E02-gh-pages)
      - name: 📥 Checkout du code
        uses: actions/checkout@v4

      - name: ❤️ Utilisation de PNPM
        uses: pnpm/action-setup@v4
        with:
          version: 10
          run_install: false

      # Prépare l'environnement Node
      - name: ⚙️ Setup de l'environnement Node.js
        uses: actions/setup-node@v4
        # with:
          # node-version: 22

      # Installation des dépendances
      - name: 📦 Installation des dépendances
        run: pnpm install

      # Lancement des tests
      - name: 🧪 Lancement des tests
        run: pnpm test || echo "⚠️ Pas de test trouvé mais le déploiement continue…"

      # Installation de Biome (via l'action officielle)
      # → https://biomejs.dev/recipes/continuous-integration
      - name: 🚀 Installer Biome
        uses: biomejs/setup-biome@v2
        with:
          version: latest
      
      # Vérification du code avec Biome
      - name: 🎨 Exécuter Biome pour formater et analyser le code
        run: biome ci .
        # run: biome check --apply # permet d'appliquer la correction au lieu de bloquer le déploiement

      # Build du projet
      - name: 🏗️ Build du projet
        run: pnpm build

      # Téléversement des fichiers construits (`dist`) comme un artifact GH Pages
      - name: 📤 Téléversement GitHub Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  # Tâche pour déployer sur GitHub Pages
  deploy:
    needs: build # Cette tâche dépend du succès de la tâche "build"
    runs-on: ubuntu-latest
    environment:
      name: github-pages
    steps:
      # Déploiement de l'artifact précédemment créé sur GitHub Pages
      - name: 🚀 Déploiement sur GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4

       # Affichage de l'URL du site
      - name: 🔗 URL du site déployé
        run: echo "🔗 Votre site est disponible à ${{ steps.deployment.outputs.page_url }}"
```

### Configurer Vite

Par défaut, Vite génère des fichiers de _build_ relatifs à la racine du projet.  
Mais lors du déploiement, l'URL du site sera quelque chose comme
`https://<ton-utilisateur>.github.io/<nom-du-repository>/`
(et non `https://<ton-utilisateur>.github.io`).

Il faut donc définir la « base » du site pour que les ressources aient le bon chemin relatif :

```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],
  base: "/<NOM_DU_DÉPÔT>/", // remplacer ici
  // server: {
  //   host: true,
  //   watch: {
  //     usePolling: true
  //   }
  // }
});
```

### Utilisation

Pour déclencher le workflow, que faut-il faire ?  
→ _push_ sur la branche `S19E02-gh-pages` !

### Activation des GH Pages

On n'oublie pas d'activer les GH Pages… :  
`Settings > Pages` : choisir la branche `gh-pages` et enregistrer.

### Erreur lors du déploiement

- « branch 'S19E02-gh-pages' is not allowed to deploy to github-pages due to environment protection rules »  
  `Settings > Environment > github-pages`

- problème de JS, CSS (404) ? → ne pas oublier la `base` dans la configuration de Vite
