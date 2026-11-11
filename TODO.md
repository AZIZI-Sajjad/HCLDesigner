# Checklist Terraform / Ansible – MediTrack

## Phase 1 : finir le Terraform

### S3
- [x] Créer le bucket (nom unique au niveau mondial)
- [x] Bloquer tout accès public au bucket
- [x] Activer le chiffrement du bucket (SSE-S3 ou ta clé KMS)
- [x] Activer le versioning (optionnel, mais utile à valoriser pour la question 5)
- [x] Prévoir l'upload des fichiers du site (`index.html`, CSS, images) avec le bon `content_type`

### CloudFront
- [ ] Créer un OAC (Origin Access Control)
- [ ] Créer la distribution avec le bucket S3 comme origine, rattachée à l'OAC
- [ ] Définir `index.html` comme objet racine par défaut
- [ ] Viewer protocol policy : forcer la redirection vers HTTPS
- [ ] Certificat : celui par défaut de CloudFront (pas de domaine personnalisé)
- [ ] Ajouter une bucket policy qui autorise uniquement cette distribution à lire le bucket
- [ ] Ajouter dans la policy de la clé KMS du bucket l'autorisation pour CloudFront de déchiffrer

### EC2
- [ ] Chiffrer le disque root (`root_block_device`)
- [ ] Vérifier que le volume de 2 Go utilise bien ta clé KMS

### Outputs
- [ ] URL CloudFront
- [ ] IP publique (EIP) de l'EC2
- [ ] Nom du bucket

### Nettoyage
- [ ] Supprimer `data.aws_vpc.default` s'il ne sert plus
- [ ] Corriger les descriptions du SG (« all ip » → ce qui est vraiment autorisé)
- [ ] Renommer `alias/prod-main` et le tag `Environment = prod`
- [ ] Renommer les ressources `example` et `aws_caller_identity_ent_ent`
- [ ] Remplacer `eu-west-3a` écrit 3 fois par une variable ou une référence
- [ ] Retirer le `/` final de `var_file_index_html` dans `aws.tfvars`
- [ ] `aws.tfvars` dans le `.gitignore`, `aws.tfvars.example` commité

## Phase 2 : vérifier avant l'apply

```bash
terraform fmt -recursive
terraform validate
terraform plan -var-file=aws.tfvars -out=tfplan
```

- [ ] Relire le plan
- [ ] Le plan contient bien S3, CloudFront, OAC, la bucket policy, l'EC2, le VPC et KMS

## Phase 3 : apply

```bash
terraform apply tfplan | tee logs/terraform-apply.log
```

- [ ] Sortie complète sauvegardée dans un fichier log (livrable de la question 4)
- [ ] Attendre que la distribution CloudFront soit déployée (ça prend plusieurs minutes)

## Phase 4 : tests (livrable de la question 4)

```bash
curl -I http://<url-cloudfront>
curl -I https://<url-cloudfront>
curl -I https://<bucket>.s3.eu-west-3.amazonaws.com/index.html
```

- [ ] `https://<url-cloudfront>` affiche le site
- [ ] `http://<url-cloudfront>` redirige vers HTTPS (code 301)
- [ ] L'URL directe du bucket S3 renvoie 403 (le bucket n'est pas public)
- [ ] SSH vers l'EC2 depuis ton IP : OK
- [ ] Volumes chiffrés dans la console EC2 → Volumes, avec la colonne Encrypted à « Yes »

## Phase 5 : Ansible (question 3)

- [ ] L'inventaire pointe vers l'EIP, récupérée depuis l'output Terraform
- [ ] Le playbook installe et démarre Nginx ou Apache
- [ ] Le playbook déploie les fichiers statiques dans le DocumentRoot
- [ ] UFW : deny par défaut, autoriser 22 et 443 (80 si besoin), puis activer
- [ ] Créer un utilisateur dédié, désactiver la connexion SSH root et l'authentification par mot de passe

```bash
ansible-playbook -i inventory.ini site.yml --check
ansible-playbook -i inventory.ini site.yml | tee logs/ansible-run.log
```

- [ ] Relancer le playbook : il doit être idempotent (`changed=0`)

## Phase 6 : livrables

- [ ] Question 1 : compte-rendu d'installation, avec l'utilisateur IAM et la politique de moindre privilège (VPC, EC2, S3, CloudFront, KMS)
- [ ] Question 2 : code commenté et README
- [ ] Question 4 : logs Terraform et Ansible, et l'URL CloudFront
- [ ] Question 5 : rapport PDF avec les captures (console S3, CloudFront, EC2, volumes chiffrés, site en HTTPS), section IAM / chiffrement / TLS
- [ ] Dépôt GitHub public : aucun secret, ni `tfvars`, ni `.tfstate`, ni clé SSH

```bash
terraform destroy -var-file=aws.tfvars
```

- [ ] `terraform destroy` après les captures, pour éviter les coûts