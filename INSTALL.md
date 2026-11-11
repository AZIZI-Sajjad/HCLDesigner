# Installation de HCLDesigner

Prérequis : Debian/Ubuntu, root, `git` et `unzip`. Docker est installé par le script si absent.

## Installation

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
Sauvegarder `/opt/hcldesigner/hcldesigner/.env` hors du serveur : il contient la clé maître.

Un worker par défaut est installé et enrôlé automatiquement (Administration > Workers).

## Ajouter un worker

Administration > Workers > Ajouter un worker, puis lancer la commande générée sur la machine du worker :

```bash
curl -fsSL http://<ip>:8080/api/agent/install.sh | sudo bash -s -- --url http://<ip>:8080 --token hcldj_...
```

Logs : `journalctl -u hcldesigner-agent -f`
Désinstallation : `curl -fsSL http://<ip>:8080/api/agent/install.sh | sudo bash -s -- --uninstall`

## Mise à jour

Le `.env` et les données sont conservés.

```bash
cd ~/hcldesigner && git pull && cd v3/
cp hcldesigner.zip /opt/hcldesigner/
cd /opt/hcldesigner
unzip -o hcldesigner.zip
cd hcldesigner
./install.sh
```

Puis Ctrl+Shift+R dans le navigateur.

Workers distants : Workers > Réenrôler génère une commande ; lancée sur la machine, elle remplace le binaire et garde l'identité du worker. Pour changer de jeton (machine compromise), lancer d'abord `--uninstall`, puis la commande.

## Réinitialisation / désinstallation

```bash
cd /opt/hcldesigner/hcldesigner
./install.sh --reset                    # repartir de zéro (supprime les données)
docker compose down -v --rmi all && rm -rf /opt/hcldesigner   # désinstaller
```
