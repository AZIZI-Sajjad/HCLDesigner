## HCLDesigner v2

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
- Ancienne installation incompatible : réinstaller avec `./install.sh --reset`