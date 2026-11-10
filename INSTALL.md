# Installation de HCLDesigner

Prérequis : Debian/Ubuntu, root, `git` et `unzip`. Docker est installé par le script si absent.

## Installation

```bash
git clone https://github.com/AZIZI-Sajjad/hcldesigner.git
cd hcldesigner/v2/
mkdir -p /opt/hcldesigner && cp hcldesigner.zip /opt/hcldesigner/
cd /opt/hcldesigner
unzip -o hcldesigner.zip
cd hcldesigner
./install.sh
```

Accès : `http://<ip>:8080`, compte `admin`, mot de passe affiché à la fin du script.
Sauvegarder `/opt/hcldesigner/hcldesigner/.env` hors du serveur : il contient la clé maître.

## Mise à jour

Le `.env` et les données sont conservés.

```bash
cd ~/hcldesigner && git pull && cd v2/
cp hcldesigner.zip /opt/hcldesigner/
cd /opt/hcldesigner
unzip -o hcldesigner.zip
cd hcldesigner
./install.sh
```

Puis Ctrl+Shift+R dans le navigateur.

## Réinitialisation / désinstallation

```bash
cd /opt/hcldesigner/hcldesigner
./install.sh --reset                    # repartir de zéro (supprime les données)
docker compose down -v --rmi all && rm -rf /opt/hcldesigner   # désinstaller
```