# Tester une mise à jour locale avec morfMonitor

Ce guide permet de vérifier, sur une machine Linux ou un Raspberry Pi déjà
équipé de morfSystem, le bouton **Mettre à jour** de morfMonitor.

Le test met à jour un seul service de cette machine. Il ne donne pas à
morfMonitor le droit d'administrer le réseau : le navigateur parle à
morfMonitor, puis morfMonitor parle uniquement à l'agent local morfUpdate sur
`127.0.0.1:8794`.

> Ce guide part d'une installation vierge de la fonction de mise à jour, pas
> d'une machine vierge de Linux. Installer d'abord les outils et les services
> avec le [guide de démarrage de morfTools](https://github.com/morfredus/morfTools/blob/main/docs/GUIDE-DEMARRAGE.md).

## Ce qui sera installé

Trois rôles restent distincts :

| Élément | Rôle | À installer pour ce test |
| --- | --- | --- |
| morfMonitor | affiche les versions et transmet la demande | oui |
| morfUpdate | vérifie puis installe le paquet localement | oui |
| morfCollector | service volontairement ancien à mettre à jour | déjà installé |

Le test ne demande pas de réinstaller tout le parc. Une machine où
`morfCollector` est encore en `0.5.1` et dont la release `0.7.0` contient un
paquet Linux ARM64 est un bon exemple.

## Avant de commencer

Préparer les éléments suivants :

- une machine Linux ARM64 avec un accès Internet ;
- morfMonitor accessible sur son port habituel `8790` ;
- un paquet `morfupdate` récent et un paquet `morfmonitor` récent dans le
  dossier `dist` de votre workspace ;
- une release GitHub contenant le paquet du service à tester et son manifeste ;
- un compte disposant du droit de lire cette release GitHub.

Les exemples ci-dessous utilisent `morfCollector`. Remplacer seulement les
quatre valeurs indiquées dans le tableau si vous souhaitez tester un autre
service.

| Valeur | Exemple morfCollector |
| --- | --- |
| Projet | `morfCollector` |
| Unité systemd | `morfcollector` |
| Dépôt GitHub | `morfredus/morfCollector` |
| Adresse de santé | `http://127.0.0.1:8792/healthz` |

Ne pas choisir morfUpdate comme cible : l'agent refuse de se mettre à jour
lui-même dans cette première version.

## 1. Installer les deux briques nécessaires

Depuis le dossier `dist` qui contient les paquets construits pour le Raspberry
Pi, installer morfUpdate et morfMonitor. Adapter les numéros de version aux
fichiers réellement présents.

```bash
cd ~/Codage/morfSystem/dist
sudo apt install ./morfupdate-0.3.4-linux-arm64.deb ./morfmonitor-0.13.1-linux-arm64.deb
```

Vérifier ensuite que les deux services existent :

```bash
sudo systemctl status morfupdate --no-pager
sudo systemctl status morfmonitor --no-pager
```

Ne pas poursuivre si l'un des deux affiche `failed`. Lire alors son journal,
qui donne la cause exacte :

```bash
sudo journalctl -u morfupdate -n 50 --no-pager
sudo journalctl -u morfmonitor -n 50 --no-pager
```

## 2. Créer un jeton GitHub en lecture seule

morfUpdate télécharge les releases depuis GitHub. Un jeton personnel est
nécessaire pour les dépôts privés et reste conseillé pour éviter les limites
d'accès anonyme.

1. Ouvrir [GitHub - Fine-grained personal access tokens](https://github.com/settings/personal-access-tokens/new).
2. Donner un nom explicite, par exemple `morfUpdate pi4fred`.
3. Choisir le propriétaire `morfredus` et limiter le jeton aux seuls dépôts qui
   pourront être mis à jour. Pour ce premier test, sélectionner `morfCollector`.
4. Dans **Repository permissions**, donner **Contents: Read-only**.
5. Créer le jeton puis le copier immédiatement. GitHub ne l'affiche plus après
   avoir quitté cette page.

Créer le fichier qui le protégera sur la machine :

```bash
sudoedit /etc/morfsystem/morfupdate/github-read.token
```

Coller uniquement le jeton GitHub dans ce fichier, sans guillemet et sans texte
supplémentaire. Enregistrer, puis restreindre sa lecture à root et au compte de
service morfUpdate :

```bash
sudo chown root:morfupdate /etc/morfsystem/morfupdate/github-read.token
sudo chmod 640 /etc/morfsystem/morfupdate/github-read.token
```

Ne jamais placer ce jeton dans un dépôt Git, un fichier de configuration partagé,
un journal ou une capture d'écran.

## 3. Autoriser explicitement un seul service

Ouvrir la configuration réellement lue par le service. Modifier un fichier dans
le clone Git ne modifie jamais le service installé.

```bash
sudoedit /etc/morfsystem/morfupdate/morfupdate.json
```

Conserver les premières lignes et remplacer le contenu complet par ce modèle,
en adaptant seulement les valeurs du tableau plus haut si nécessaire :

```json
{
  "http_port": 8794,
  "bind_address": "127.0.0.1",
  "token_file": "@morfupdate-state@/api.token",
  "github_token_file": "@morfupdate-config@/github-read.token",
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

Cette liste est une barrière de sécurité : une demande concernant un autre
projet est refusée. Elle ne doit contenir ni URL de téléchargement, ni commande,
ni chemin local.

Redémarrer l'agent afin qu'il crée son jeton local aléatoire et relise sa
configuration :

```bash
sudo systemctl enable --now morfupdate
sudo systemctl restart morfupdate
curl http://127.0.0.1:8794/healthz
```

La dernière commande doit répondre avec un état de santé. Elle ne nécessite pas
de jeton car elle ne modifie rien.

## 4. Donner le jeton local à morfMonitor, sans l'exposer

Le jeton généré par morfUpdate appartient uniquement à son compte de service.
Pour que morfMonitor puisse transmettre la demande locale, créer une copie
spécifique, lisible seulement par le **compte qui exécute réellement l'unité
systemd**. Ce compte n'est pas nécessairement nommé `morfmonitor` : il dépend de
l'installation de la machine.

Détecter son nom, puis le conserver dans la même fenêtre de terminal pour les
commandes qui suivent :

```bash
MONITOR_USER="$(sudo systemctl show --value --property=User morfmonitor)"
test -n "$MONITOR_USER" || {
  echo "Compte systemd de morfMonitor introuvable"
  exit 1
}
echo "morfMonitor s'exécute avec : $MONITOR_USER"
```

Créer alors le fichier avec les droits de ce compte :

```bash
sudo install \
  -o root \
  -g "$MONITOR_USER" \
  -m 640 \
  /var/lib/morfsystem/morfupdate/api.token \
  /etc/morfsystem/morfmonitor/morfupdate-api.token
```

Ne pas modifier les permissions du jeton original et ne pas utiliser `setfacl`
sur ce fichier. morfUpdate vérifie volontairement qu'il ne peut pas être lu par
un groupe ; ouvrir cette permission empêcherait l'agent de démarrer.

Ouvrir ensuite la configuration installée de morfMonitor :

```bash
sudoedit /etc/morfsystem/morfmonitor/morfmonitor.json
```

Remplacer, ou ajouter, le bloc `update_agent` existant par :

```json
"update_agent": {
  "enabled": true,
  "token_file": "/etc/morfsystem/morfmonitor/morfupdate-api.token"
},
```

Le bloc doit rester à la racine du fichier JSON, au même niveau que
`http_port`, `bind_address` et `beacon`. Vérifier la virgule avant et après le
bloc selon sa position dans le fichier : un fichier JSON invalide empêche le
service de démarrer.

`sudoedit` peut modifier les permissions du fichier édité. Rétablir donc les
droits du compte de service sur le fichier de configuration et son dossier :

```bash
sudo chown root:"$MONITOR_USER" /etc/morfsystem/morfmonitor
sudo chmod 750 /etc/morfsystem/morfmonitor

sudo chown root:"$MONITOR_USER" \
  /etc/morfsystem/morfmonitor/morfmonitor.json \
  /etc/morfsystem/morfmonitor/morfupdate-api.token
sudo chmod 640 \
  /etc/morfsystem/morfmonitor/morfmonitor.json \
  /etc/morfsystem/morfmonitor/morfupdate-api.token

sudo -u "$MONITOR_USER" test -r \
  /etc/morfsystem/morfmonitor/morfmonitor.json \
  && sudo -u "$MONITOR_USER" test -r \
  /etc/morfsystem/morfmonitor/morfupdate-api.token \
  && echo "Configuration et jeton lisibles"
```

Redémarrer et contrôler morfMonitor :

```bash
sudo systemctl restart morfmonitor
sudo systemctl status morfmonitor --no-pager
```

## 5. Lancer le test depuis le navigateur

1. Ouvrir `http://<nom-ou-ip-de-la-machine>:8790/`.
2. Aller dans **Services morfSystem**.
3. Cliquer sur **Vérifier les versions**.
4. Attendre que `morfCollector` affiche une version plus récente et le bouton
   **Mettre à jour**.
5. Cliquer sur ce bouton et confirmer.

Le navigateur peut être sur une autre machine du réseau : cela reste une mise à
jour locale car morfMonitor communique avec `127.0.0.1:8794` sur sa propre
machine. Aucun port d'administration n'est ouvert sur le réseau.

L'agent suit : téléchargement, vérification du manifeste et du SHA-256,
vérification de provenance, installation, redémarrage et contrôle de santé. Le
service cible doit ensuite afficher sa nouvelle version et rester `active`.

Vérifier également depuis le terminal :

```bash
sudo systemctl status morfcollector --no-pager
curl http://127.0.0.1:8792/healthz
```

## Dépannage rapide

### `remote update requests are unavailable`

morfMonitor est antérieur à la version qui accepte une demande depuis
l'interface Web LAN. Installer un paquet morfMonitor récent, puis redémarrer le
service.

### `agent de mise à jour indisponible`

morfUpdate n'est pas démarré ou son port n'est pas local. Exécuter la commande
suivante, puis consulter son journal :

```bash
sudo systemctl status morfupdate --no-pager
```

### `jeton local de mise à jour inaccessible`

La copie destinée à morfMonitor est absente ou ses permissions sont incorrectes.
Reprendre exactement l'étape 4, en vérifiant les droits avec le compte affiché
par `systemctl show`, et non en supposant un nom de compte.

### Le bouton reste grisé

`update_agent.enabled` est à `false`, ou la release ne contient pas de paquet
pour l'architecture de la machine. Corriger le fichier morfMonitor puis vérifier
la release GitHub.

### La demande est refusée

Le projet n'est pas déclaré dans `targets`. Ajouter uniquement ce projet, son
unité, son dépôt et son URL de santé à l'étape 3.

### Aucune version récente n'apparaît

La release source ou le paquet n'a pas été publié. Suivre le workflow de
packaging dans morfTools, puis relancer la vérification.

### L'installation échoue après téléchargement

Le paquet, son manifeste, son checksum ou la provenance ne correspondent pas.
Lire le journal suivant :

```bash
sudo journalctl -u morfupdate -n 100 --no-pager
```

L'agent conserve le diagnostic exact.

## Ce que ce premier jalon ne fait pas

- il ne met pas automatiquement un service à jour ; l'action reste explicite ;
- il ne met pas à jour morfUpdate lui-même ;
- il n'installe pas un paquet absent de la release ou prévu pour une autre
  architecture ;
- il ne permet pas encore de mettre à jour une machine distante ;
- il ne promet pas de retour arrière automatique en cas d'échec.

Sous Windows, morfUpdate applique le même contrat et sélectionne un asset `.zip`
adapté. La séparation entre l'agent, la configuration, les jetons et morfMonitor
reste identique ; le mécanisme d'installation final utilise le gestionnaire de
services Windows au lieu de systemd. Le contrat complet est documenté dans
[l'agent morfUpdate](https://github.com/morfredus/morfUpdate/blob/main/docs/fr/AGENT-CONTRACT.md).
