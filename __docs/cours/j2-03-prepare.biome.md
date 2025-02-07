# Configurer un projet Vite avec Biome

> **NOTE** on peut configurer le projet en lui-même…
> C'est-à-dire son titre (visible dans l'onglet) et la langue d'affichage
> dans le fichier `./index.html`

## Biome

Biome est un _linter_ (comme ESLint) ET un _formatter_ (comme Prettier).

Un _formatter_ sert à formater le code dès qu'on sauvegarde le fichier.
Contrairement aux _linters_, les formateurs ne détectent pas des erreurs
ou des bugs, mais se concentrent sur la mise en forme du code :
les tabulations, les espaces, les _quotes_…

→ Le plus connu ? Certainement : Prettier

> **NOTE** Pourquoi Biome ?
>
> C'est un _linter_ + _formatter_, donc moins de paquets à gérer
> et plus facile à configurer.
>
> Il supporte de [multiples langages](https://biomejs.dev/internals/language-support/) :
> JS, TS, JSON, JSX, CSS (et bientôt HTML).
>
> Et, il est beaucoup plus rapide !

### Préparation

Par défaut, Vite installe ESLint (dans une configuration minimale)…
Il faut donc supprimer toutes les références à ESLint :

- dépendances
- scripts
- fichier de configuration

> **ATTENTION** les dépendances sont définies dans le `package.json` et
> sont installées dans les `node_modules/`…
>
> Pour bien supprimer :
>
> - soit `pnpm uninstall XXX YYY ZZZ`
> - soit on modifie le `package.json`, on supprime le `pnpm-lock.yaml` et
>   `node_modules/`, puis on réinstalle les dépendances avec `pnpm i`

### Installation

→ <https://biomejs.dev/guides/getting-started/>

1. Ajout de la dépendance : `pnpm add --save-dev --save-exact @biomejs/biome`

   > **NOTE** `--save-exact` installe exactement la dernière version ;
   > préconisé par Biome

2. Initialisation : `pnpm biome init` → `biome.json`

3. Configuration :

    ```json
    {
      "$schema": "https://biomejs.dev/schemas/1.9.4/schema.json",
      "vcs": {
        "enabled": false,
        "clientKind": "git",
        "useIgnoreFile": false
      },
      "files": {
        "ignoreUnknown": false,
        "ignore": []
      },
      "formatter": {
        "enabled": true,
        "indentStyle": "space"
      },
      "organizeImports": {
        "enabled": true
      },
      "linter": {
        "enabled": true,
        "rules": {
          "recommended": true,
          "style": {
            "noNonNullAssertion": "off"
          },
          "suspicious": {
            "noShadowRestrictedNames": "off",
            "noDuplicateParameters": "off"
          },
          "correctness": {
            "noUnusedImports": "error"
          }
        },
        "ignore": ["dist"]
      },
      "javascript": {
        "formatter": {
          "quoteStyle": "single"
        }
      },
      "overrides": [
        {
          "include": ["./tsconfig.app.json", "./tsconfig.node.json"],
          "json": {
            "parser": {
              "allowComments": true
            }
          }
        }
      ]
    }
    ```

4. Ajouter les scripts

    ```json
    {
      "scripts": {
        "format": "pnpx @biomejs/biome format ./",
        "format:fix": "pnpx @biomejs/biome format --write ./",
        "lint": "pnpx @biomejs/biome lint ./",
        "lint:fix": "pnpx @biomejs/biome lint --write ./",
        "check": "pnpx @biomejs/biome check ./",
        "check:fix": "pnpx @biomejs/biome check --write ./"
      }
    }
    ```

5. Configurer VSCode `.vscode/settings.json`

    ```json
    {
      "editor.codeActionsOnSave": {
        "quickfix.biome": "explicit",
        "source.organizeImports.biome": "explicit"
      },
      "editor.defaultFormatter": "biomejs.biome",
      "editor.formatOnSave": true
    }
    ```

    > <https://biomejs.dev/reference/vscode/>

6. Installer l'[extension VScode](https://marketplace.visualstudio.com/items?itemName=biomejs.biome)
