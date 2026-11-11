# HCLDesigner

Concevoir, générer et exécuter du code Terraform / OpenTofu depuis un canvas web.

On place des ressources, on les relie, HCLDesigner écrit le HCL. Les plans, applies et destroys passent par des workers, avec approbations, gestion du state, planification et contrôle d'accès par rôles. Le code peut être importé depuis Git et y être poussé.

## Fonctionnalités

| | |
|---|---|
| **Canvas** | Tous les providers Terraform (schéma lu depuis le provider), liaisons guidées avec arguments obligatoires et valeurs par défaut, cadres imbriqués (VPC > subnet > instance), annuler / rétablir, mise en page automatique |
| **Code** | Génération déterministe, un seul fichier ou découpage (`main.tf`, `variables.tf`, `ec2.tf`, `s3.tf`…), aperçu en direct, blocs `import` / `moved` |
| **Import** | Code Terraform existant (`.tf`, `.zip` ou dépôt Git, sous-dossier compris) converti en objets et liens |
| **Modules** | Modules internes versionnés, extraction d'une sélection en module, modules registry / Git |
| **Exécution** | init, validate, plan, apply du plan sauvegardé, destroy ; logs en direct, secrets masqués, plans chiffrés |
| **Workers** | Agents sans accès à la base, installés sur n'importe quel Linux par une commande générée ; pause, révocation, slots, labels par site |
| **State** | Backend interne chiffré et versionné, S3, AzureRM, GCS, HTTP, GitLab, PostgreSQL, Consul, Kubernetes… ; explorateur, migration entre backends |
| **Git** | GitHub, GitLab, Gitea / Forgejo (SaaS ou auto-hébergé) ; connexions personnelles ; push direct ou pull / merge request |
| **Gouvernance** | Rôles personnalisés, approbations, fenêtres de modification, règles de conformité bloquantes, audit immuable, MFA |
| **Automatisation** | Détection de drift, plans et applies planifiés, destroy des environnements éphémères, notifications mail / Teams / Slack |
| **Tableaux** | Colonnes déplaçables et redimensionnables, filtres multichoix, vues enregistrées, export CSV / HTML |

## Installation

Serveur Debian / Ubuntu, root. Docker est installé par le script si absent.

```bash
git clone https://github.com/AZIZI-Sajjad/hcldesigner.git
cd hcldesigner/v3/
mkdir -p /opt/hcldesigner && cp hcldesigner.zip /opt/hcldesigner/
cd /opt/hcldesigner
unzip -o hcldesigner.zip
cd hcldesigner
./install.sh
```

Accès : `http://<ip>:8080`, compte `admin`, mot de passe affiché à la fin du script.

Ajouter un worker : **Administration → Workers → Ajouter un worker**, puis lancer la commande générée sur la machine cible.

## Architecture

```
Navigateur ── nginx ── API (Go) ── PostgreSQL
                         │  └── scheduler (cron, notifications)
                         │
             workers (agents HTTP) ── Terraform / OpenTofu ── clouds, vSphere, VCD, Proxmox…
                         │
             GitHub / GitLab / Gitea
```

- **Backend** : Go, chi, pgx, hclwrite / hclsyntax
- **Frontend** : React, TypeScript, React Flow, Monaco, TanStack Table, Tailwind
- **Données** : PostgreSQL (file de jobs, verrous), secrets chiffrés AES-256-GCM
- **Workers** : un seul binaire (`hcldesigner agent`), jeton propre et révocable, service systemd non root

## Documentation

- [INSTALL.md](INSTALL.md) : installation, mise à jour, workers
- [docs/architecture.md](docs/architecture.md) : composants, flux, sécurité
- [docs/regles-connexion.md](docs/regles-connexion.md) : règles de liaison par provider
- [docs/openapi.yaml](docs/openapi.yaml) : API REST
- [RELEASE_NOTES.md](RELEASE_NOTES.md) : nouveautés

## Marques

Terraform est une marque déposée de HashiCorp, Inc. OpenTofu est une marque de la Linux Foundation. HCLDesigner est un projet indépendant, sans lien avec HashiCorp ni la Linux Foundation.