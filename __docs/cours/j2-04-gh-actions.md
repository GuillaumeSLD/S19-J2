# GitHub Actions

## Introduction

Certaines étapes de note CI/CD peuvent se faire grâce aux
[_Hooks_ de Git](https://git-scm.com/book/fr/v2/Personnalisation-de-Git-Crochets-Git)

Exemple :
avant de pouvoir _commit_, on vérifie le style du code (Biome) et
on lance les tests unitaires

```bash
#!/bin/sh
# @file .git/hooks/pre-commit
# @run chmod +x .git/hooks/pre-commit

# Vérifier le style du code
pnpm check:fix

# Exécuter les tests unitaires
# pnpm test

# Si l'une des commandes échoue, annuler le commit
if [ $? -ne 0 ]; then
  echo "Les vérifications pré-commit ont échoué. Commit annulé."
  exit 1
fi
```

Avantages : rapide et bloque les évènements Git (commit, push, merge…)

Inconvénient : **non partagé** entre les développeurs → uniquement local.

## Définition

Service d'automatisation qui exécute des _workflows_ lorsque certains évènements
se produisent sur un dépôt GitHub.

### Fonctionnement

- Définis dans `.github/workflows/` sous forme de fichiers YAML.
- Exécutés sur les serveurs GitHub.
- Déclenchés par des événements comme _push_, _pull request_, _issue_…
- Utilisent des _runners_ (machines virtuelles) pour exécuter des actions.

### Exemples d'utilisation

- Exécuter des tests CI/CD après un push (`on: push`).
- Déployer une application après une PR fusionnée (`on: pull_request`).
- Lancer une analyse de sécurité après une modification (`on: pull_request`).
- Automatiser la gestion des issues (`on: issue_opened`).

### Avantages

- Partagé entre tous les contributeurs du projet.
- S'exécute sur GitHub, pas besoin d'installation locale.
- Fonctionne avec des services externes (Docker, AWS, Slack…).
- Peut automatiser des tâches au-delà de Git (gestion de projet, notifications, CI/CD).

### Inconvénients

- Plus lent que Git Hooks (exécuté à distance).
- Dépend de GitHub (et de ses limitations gratuites).
- Ne bloque pas un commit avant qu'il soit poussé (contrairement aux Git Hooks).

### Utilisation

- Onglet « **Actions** » d'un dépôt GitHub.
- GH propose de nombreux **_workflows_ pré-configurés**.
- Possibilité de créer des **workflows personnalisés**
  - GH propose des **actions pré-configurées** (_Setup Node.js environnement_…)

## Exemple

- [Déploiement](./05-deployment.md)
