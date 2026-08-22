# Tester une mise à jour locale avec morfMonitor

Ce guide permet de vérifier, depuis un navigateur du réseau local, le bouton
**Mettre à jour** de morfMonitor. La mise à jour reste locale à la machine qui
héberge morfMonitor : le navigateur parle au port 8790, puis morfMonitor parle
uniquement à morfUpdate sur `127.0.0.1:8794`.

Les releases publiques GitHub sont lues anonymement. Il ne faut créer ni jeton
GitHub, ni jeton local, ni modifier les permissions de morfMonitor pour ce
parcours.

> Tous les services du parc peuvent être mis à jour depuis morfMonitor, **sauf
> morfUpdate** (l'agent refuse de se mettre à jour lui-même : l'installer avec
> `apt` / `service.py install`, jamais le bouton). Le bouton envoie le nom du
> **dépôt GitHub** (`morfDashboard`), jamais le libellé affiché (`DashBoard`)
> ni l'unité systemd (`morfdashboard`).

## Chaîne et blocages

```text
Navigateur LAN
    POST /api/updates  {project, version}     port 8790
        ▼
morfMonitor  (update_agent.enabled)
    POST http://127.0.0.1:8794/api/v1/updates
        ▼
morfUpdate
    project ∈ targets ?  sinon 400
    une opération à la fois ?  sinon 409
        ▼
GitHub release vX.Y.Z + manifest.json + SHA-256 + tag
        ▼
morfupdate-helper (setuid)  dpkg --install  +  systemctl restart
        ▼
GET health_url  →  200
```

Blocages fréquents, dans l'ordre où ils se voient :

| Symptôme | Cause | Que faire |
| --- | --- | --- |
| `agent de mise à jour indisponible` | morfupdate arrêté, pas sur 8794, ou `update_agent.enabled` false | `curl http://127.0.0.1:8794/healthz` ; `systemctl status morfupdate` ; bloc dans morfmonitor.json |
| `project and version must be declared identifiers` | `project` absent de `targets`, ou ancien bouton qui envoyait le libellé (`DashBoard`) | Vérifier `/etc/morfsystem/morfupdate/morfupdate.json` ; morfMonitor >= 0.14.3 |
| Un seul service se met à jour | `targets` n'a qu'une entrée (essai initial). `service.py update` ne fusionne **pas** les listes | `sudo python3 service.py config push --force` depuis le clone morfUpdate, puis `systemctl restart morfupdate` |
| `privileged helper failed` | helper absent ou setuid / Qt | morfUpdate >= 0.4.2, `sync-morf.sh`, réinstaller |
| `dpkg failed` (le même `.deb` s'installe avec `sudo`) | le helper n'avait que l'euid root ; `dpkg` exige `getuid()==0` | morfUpdate >= 0.4.4, réinstaller le helper (`apt install` du `.deb` ou `service.py install`) |
| `another update is active` | une opération n'est pas finie | attendre, ou lire l'id dans la 409 |
| Release / manifest / SHA | pas de `.deb` linux-arm64 (ou amd64) dans la release | republier le paquet |
| `service health check failed` | l'unité redémarre trop lentement, ou `/healthz` n'est pas celui déclaré | journalctl de l'unité ; coller `health_url` sur le vrai port du registre |
| Bouton invisible | pas d'état « Mise à jour disponible » | version exécutée déjà égale à la release, ou beacon sans version, ou pas de `repo` dans morfsystem.json |

## Avant de commencer

Prévoir :

- une machine Linux ou Raspberry Pi avec accès Internet ;
- les services déjà installés, au moins un en version plus ancienne que sa release ;
- une release GitHub publique contenant le `.deb` correspondant, `manifest.json`
  et les checksums ;
- morfMonitor 0.14.4 ou plus récent et morfUpdate 0.4.5 ou plus récent.

Les cibles autorisées (hors morfUpdate) :

| Projet | Unité | Santé |
| --- | --- | --- |
| morfAnalytics | morfanalytics | `http://127.0.0.1:8799/healthz` |
| morfCollector | morfcollector | `http://127.0.0.1:8792/healthz` |
| morfDashboard | morfdashboard | `http://127.0.0.1:8791/healthz` |
| morfMonitor | morfmonitor | `http://127.0.0.1:8790/healthz` |
| morfNotify | morfnotify | `http://127.0.0.1:8789/healthz` |
| morfPhoto | morfphoto | `http://127.0.0.1:8793/healthz` |
| morfSensor | morfsensor | `http://127.0.0.1:8788/healthz` |
| morfSync | morfsync | `http://127.0.0.1:8080/healthz` |

## 1. Installer ou mettre à niveau les deux briques

La voie recommandée consiste à installer les paquets construits pour la
machine. Depuis le dossier qui les contient :

```bash
cd ~/Codage/morfSystem/dist
sudo apt install ./morfupdate-0.4.5-linux-arm64.deb
curl -sS http://127.0.0.1:8794/healthz
sudo apt install ./morfmonitor-0.14.4-linux-arm64.deb
```

Installer **morfUpdate d'abord**, puis morfMonitor. L'agent ne peut pas se
mettre à jour lui-même ; sans 0.4.5, un `/healthz` trop lent affiche un échec
alors que le paquet est déjà posé. Adapter les noms de fichiers à `ls`.

Un `apt` n'écrase pas forcément `/etc/morfsystem/morfsystem.json`. Après un
Monitor neuf, depuis le clone :

```bash
cd ~/Codage/01-Travail/morfMonitor
./scripts/linux/deploy-config.sh --shared
```

Le paquet morfUpdate installe aussi son helper privilégié, hors du répertoire
de l'agent.

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

## 3. Autoriser les services à mettre à jour

La référence est `config/morfupdate.example.json` du clone morfUpdate : tous les
services du tableau ci-dessus. Sur une **nouvelle** installation, ce fichier est
copié vers `/etc/morfsystem/morfupdate/morfupdate.json`.

Sur une machine qui n'avait qu'**une** cible de test, cette liste n'est **pas**
complétée par `service.py update`. Il faut pousser la référence :

```bash
cd ~/Codage/01-Travail/morfUpdate
sudo python3 service.py config push --force
sudo systemctl restart morfupdate
```

Vérifier ensuite que le JSON déployé contient bien plusieurs objets dans
`targets` (pas seulement `morfCollector`) :

```bash
python3 -c "import json; p='/etc/morfsystem/morfupdate/morfupdate.json'; d=json.load(open(p)); print(len(d['targets']), [t['project'] for t in d['targets']])"
```

Ne mettre ni URL de téléchargement, ni commande, ni chemin local dans `targets` :
l'agent les déduit de la release contrôlée. `health_url` est la seule URL, et
elle pointe toujours vers la machine locale.

```bash
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
3. Attendre qu'un service affiche une release plus récente.
4. Cliquer sur **Mettre à jour** et confirmer. Le projet demandé est le dépôt
   GitHub, pas le libellé de la colonne Service.

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

Les autres confrontations au parc (compilation, découverte, clients multiples)
sont dans [Validation réelle](VALIDATION-REELLE-MORFSYSTEM.md).
