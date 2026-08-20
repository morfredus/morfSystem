# Tester une mise à jour locale avec morfMonitor

Ce guide permet de vérifier, depuis un navigateur du réseau local, le bouton
**Mettre à jour** de morfMonitor. La mise à jour reste locale à la machine qui
héberge morfMonitor : le navigateur parle au port 8790, puis morfMonitor parle
uniquement à morfUpdate sur `127.0.0.1:8794`.

Les releases publiques GitHub sont lues anonymement. Il ne faut créer ni jeton
GitHub, ni jeton local, ni modifier les permissions de morfMonitor pour ce
parcours.

> Commencer avec un seul service, par exemple morfCollector. Ne jamais choisir
> morfUpdate : l'agent refuse de se mettre à jour lui-même dans ce premier jalon.

## Avant de commencer

Prévoir :

- une machine Linux ou Raspberry Pi avec accès Internet ;
- morfCollector déjà installé, dans une version plus ancienne que sa release ;
- une release GitHub publique contenant le `.deb` correspondant, `manifest.json`
  et les checksums ;
- morfMonitor 0.14.0 ou plus récent et morfUpdate 0.4.1 ou plus récent.

Les exemples utilisent cette cible :

| Valeur | morfCollector |
| --- | --- |
| Projet | `morfCollector` |
| Unité systemd | `morfcollector` |
| Dépôt GitHub | `morfredus/morfCollector` |
| Santé | `http://127.0.0.1:8792/healthz` |

## 1. Installer ou mettre à niveau les deux briques

La voie recommandée consiste à installer les paquets construits pour la
machine. Depuis le dossier qui les contient :

```bash
cd ~/Codage/morfSystem/dist
sudo apt install ./morfupdate-0.4.1-linux-arm64.deb ./morfmonitor-0.14.0-linux-arm64.deb
```

Adapter les numéros aux fichiers réellement présents. Le paquet morfUpdate
installe aussi son helper privilégié, hors du répertoire de l'agent.

Pour travailler depuis les clones, resynchroniser d'abord la copie de
morfDeploy puis installer chaque service. Lancer les commandes depuis le clone
de morfUpdate :

```bash
./scripts/sync-morf.sh
sudo python3 service.py install
```

Faire de même dans le clone de morfMonitor avec son propre `service.py install`.

Vérifier les deux services :

```bash
sudo systemctl status morfupdate morfmonitor --no-pager
curl http://127.0.0.1:8794/healthz
```

Ne pas poursuivre si un service est `failed`. Le journal donne le diagnostic :

```bash
sudo journalctl -u morfupdate -n 80 --no-pager -l
sudo journalctl -u morfmonitor -n 80 --no-pager -l
```

## 2. Nettoyer une ancienne configuration à jetons

Cette étape ne concerne que les essais réalisés avec une version antérieure de
morfUpdate. Les trois fichiers suivants ne servent plus. Les supprimer est sans
danger pour GitHub, SSH et les autres services :

```bash
sudo rm -f /var/lib/morfsystem/morfupdate/api.token
sudo rm -f /etc/morfsystem/morfupdate/github-read.token
sudo rm -f /etc/morfsystem/morfmonitor/morfupdate-api.token
```

Les anciennes clés `token_file` ou `github_token_file` peuvent rester dans les
JSON existants : les versions récentes ne les lisent plus. Ne pas supprimer les
clés SSH ni la configuration `gh` de GitHub.

## 3. Autoriser explicitement le service à mettre à jour

Ouvrir la configuration réellement utilisée par morfUpdate :

```bash
sudoedit /etc/morfsystem/morfupdate/morfupdate.json
```

Conserver `http_port` et `bind_address`, puis placer cette cible dans `targets`.
Ne mettre ni URL de téléchargement, ni commande, ni chemin local dans ce
fichier : l'agent les déduit uniquement de la release contrôlée.

```json
{
  "http_port": 8794,
  "bind_address": "127.0.0.1",
  "targets": [
    {
      "project": "morfCollector",
      "service": "morfcollector",
      "repository": "morfredus/morfCollector",
      "health_url": "http://127.0.0.1:8792/healthz"
    }
  ]
}
```

Redémarrer l'agent :

```bash
sudo systemctl restart morfupdate
curl http://127.0.0.1:8794/healthz
```

## 4. Activer le bouton dans morfMonitor

La configuration de morfMonitor doit contenir ce bloc à la racine du JSON :

```json
"update_agent": {
  "enabled": true
}
```

Si un ancien bloc contient aussi `token_file`, il peut être conservé pour le
moment : il est ignoré. Vérifier la validité du JSON puis redémarrer :

```bash
sudo systemctl restart morfmonitor
sudo systemctl status morfmonitor --no-pager
```

## 5. Lancer le test depuis le navigateur

1. Ouvrir `http://<nom-ou-ip-de-la-machine>:8790/`.
2. Aller dans **Services morfSystem** puis cliquer sur **Vérifier les versions**.
3. Attendre que morfCollector affiche une release plus récente.
4. Cliquer sur **Mettre à jour** et confirmer.

L'interface attend le résultat réel : téléchargement, contrôle du manifeste,
SHA-256, provenance du tag, installation, redémarrage et contrôle de santé.
Une opération est refusée si le projet n'est pas déclaré, si le paquet ne
correspond pas à la plateforme ou si les preuves de la release ne concordent
pas.

Après un succès, vérifier :

```bash
sudo systemctl status morfcollector --no-pager
curl http://127.0.0.1:8792/healthz
```

## Dépannage rapide

### `privileged helper failed`

La version installée de morfUpdate ne contient pas encore le helper root, ou
une installation depuis un clone n'a pas utilisé morfDeploy 0.17.1. Mettre à
jour le clone, lancer `./scripts/sync-morf.sh`, puis relancer
`sudo python3 service.py install`.

### `agent de mise à jour indisponible`

Vérifier que l'agent répond localement :

```bash
curl http://127.0.0.1:8794/healthz
sudo journalctl -u morfupdate -n 80 --no-pager -l
```

### La demande est refusée

Le projet n'est pas présent dans `targets`, ou la release ne possède pas le
paquet attendu. Vérifier le nom du projet, l'unité, le dépôt et l'URL de santé
dans la configuration de morfUpdate.

### Aucune version récente n'apparaît

La release source ou le paquet n'a pas été publié. Reprendre le workflow de
packaging dans morfTools, puis relancer **Vérifier les versions**.

### L'installation échoue après le téléchargement

Lire le diagnostic persistant :

```bash
sudo journalctl -u morfupdate -n 100 --no-pager -l
```

## Limites actuelles

- la mise à jour est toujours une action explicite ;
- morfUpdate ne se met pas à jour lui-même ;
- aucun rollback automatique n'est promis ;
- une autre machine du LAN ne peut pas encore être mise à jour à distance ;
- sous Windows, le même contrat sélectionne un `.zip`, mais la validation Linux
  est le premier parcours à réaliser.
