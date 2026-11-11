# Release notes

## v3.0.0

### Nouveautés
- Workers distants : agents sans accès à la base, ajoutés sur n'importe quel Linux par une commande générée (`curl … | sudo bash`)
- Cycle de vie des workers, worker par défaut compris : pause, reprise, révocation, réenrôlement, suppression, slots, labels
- Labels d'environnement : un run ne s'exécute que sur un worker portant les labels exigés
- Intégration Git : GitHub, GitLab, Gitea / Forgejo, en SaaS ou auto-hébergé (CA interne)
- Connexions Git personnelles : jeton et auteur des commits propres à chaque utilisateur
- Import d'un projet depuis un dépôt, code Terraform en sous-dossier compris
- Push du code généré : commit direct ou nouvelle branche + pull/merge request
- Mise à jour du modèle depuis Git
- Colonne « Worker » dans les exécutions
- Permissions `admin.workers`, `git.link`, `git.push`

### Changements
- Le conteneur `worker` lance `hcldesigner agent` et n'a plus accès à PostgreSQL ni à la clé maître
- `HCLD_WORKER_CONCURRENCY` ne fixe plus que les slots du worker par défaut à sa création
- Migration automatique de la base au démarrage (tables `workers`, `git_connections`, `project_git`)

### Mise à jour depuis v2
- `unzip -o hcldesigner.zip` puis `./install.sh` : `.env` et données conservés

## v2.0.0

### Nouveautés
- Import de code Terraform existant (`.tf` / `.zip`)
- Cadres imbriqués : VPC > subnet > instance, resource group, namespace…
- Liens tirés des expressions (pointillés)
- `install.sh` : installation, mise à jour, `--reset`
- `INSTALL.md`

### Corrections
- Glisser-déposer en HTTP
- Cache navigateur après mise à jour
- Page vide après certains imports

### Changements
- Renommage TerraCanvas → HCLDesigner (variables `HCLD_*`)
