# Guide Complet sur GitHub Actions

## 1. Concepts de Base
- **CI/CD** : Automatisation des étapes de développement (tests, build, déploiement). Réduit les erreurs humaines et accélère les itérations.
- **GitHub Actions** : Plateforme native à GitHub pour automatiser tout workflow (de la compilation au déploiement).
  - Écrit en YAML.
  - Intégré directement dans les dépôts GitHub.

---

## 2. Composants de GitHub Actions
- **Workflow** : Processus complet d’automatisation. Défini dans `.github/workflows/*.yml`.
- **Jobs** : Blocs indépendants à l’intérieur d’un workflow. Chaque job exécute une série d’étapes.
- **Steps** : Commandes ou actions spécifiques exécutées séquentiellement dans un job.
- **Actions** : Scripts réutilisables pour des tâches courantes (e.g., checkout de code, installation de dépendances).
- **Runners** : Machines (virtuelles ou self-hosted) qui exécutent les workflows. Par défaut, GitHub utilise des runners hébergés (`ubuntu-latest`, `windows-latest`, etc.).

---

## 3. Structure d’un Workflow

### Déclencheurs (Triggers)
- `on: push` : Déclenche lors d’un commit/push.
- `on: pull_request` : Déclenche sur une PR.
- `on: schedule` : Déclenche à une heure précise (CRON).

Exemple :
```yaml
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

```
### Jobs
- Chaque job peut tourner sur un environnement spécifique (`runs-on`).
- Les jobs peuvent s’exécuter :
  - **Indépendamment** : En parallèle.
  - **Séquentiellement** : Utilise le mot-clé `needs` pour indiquer les dépendances entre jobs.

Exemple :
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: echo "Déploiement effectué"
```
### Steps
- **Steps** sont des commandes ou appels d’actions à exécuter au sein d’un job.
- Exemples courants :
  - **Cloner le dépôt** :
    ```yaml
    - uses: actions/checkout@v3
    ```
  - **Installer des dépendances** :
    ```yaml
    - run: npm install
    ```



## 4. Actions : Utilisation et Création
- Les actions sont des blocs réutilisables (scripts) :
  - **Prédéfinies** : Disponibles sur le **GitHub Marketplace**.
  - **Custom** : Écrites manuellement dans un dépôt.
- Exemple d’utilisation d’une action pour configurer Node.js :
  ```yaml
  - uses: actions/setup-node@v3
    with:
      node-version: 16

## 5. Secrets et Variables d’Environnement

### Secrets
- Utilisés pour stocker des informations sensibles (clés API, mots de passe).
- Configurés dans `Settings > Secrets` du dépôt.
- Exemple d’accès dans un workflow :
  ```yaml
  env:
    API_KEY: ${{ secrets.API_KEY }}

### Variables d’Environnement
- Permettent une configuration dynamique des workflows.
- Exemple d’utilisation :
  ```yaml
  env:
    ENV_NAME: production

## 6. Exemples Pratiques

### Workflow complet de Build et Test
```yaml
name: Build and Test
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 16
      - run: npm install
      - run: npm test
```

### Déploiement Automatisé
- Avec un checkpoint manuel :
  ```yaml
  jobs:
    deploy:
      runs-on: ubuntu-latest
      environment:
        name: production
        url: https://example.com
      steps:
        - run: echo "Déploiement en cours..."

## 7. Optimisation des Workflows

### Parallélisation
- Permet d’exécuter plusieurs jobs en parallèle pour réduire les temps d’exécution.
- Exemple :
  ```yaml
  jobs:
    build:
      runs-on: ubuntu-latest
      steps: [...]
    test:
      runs-on: ubuntu-latest
      steps: [...]

### Dépendances entre Jobs
- Utilise `needs` pour définir l’ordre d’exécution des jobs.
- Exemple :
  ```yaml
  jobs:
    build:
      runs-on: ubuntu-latest
    deploy:
      needs: build
      runs-on: ubuntu-latest

### Cache
- Accélère les workflows en réutilisant des fichiers générés précédemment.
- Exemple :
  ```yaml
  - uses: actions/cache@v3
    with:
      path: ~/.npm
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

## 8. Bonnes Pratiques
- Utilise des **runners hébergés ou self-hosted** selon tes besoins.
- Stocke tous les secrets dans le **GitHub Secrets Manager**.
- Vérifie régulièrement les logs des workflows pour identifier les erreurs.
- Privilégie l’utilisation d’actions du **GitHub Marketplace** pour minimiser la maintenance.
- Teste chaque étape dans un environnement isolé avant de les intégrer au workflow principal.
