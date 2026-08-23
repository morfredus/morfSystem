# Brancher des photos réseau sur morfPhoto (sources poussées)

morfPhoto indexe des **chemins locaux** : il parcourt un dossier et lit les
métadonnées EXIF fichier par fichier. Quand les photos vivent sur un autre poste
(un portable, un fixe), il faut donc que ce dossier distant **apparaisse comme un
chemin local** sur la machine où tourne morfPhoto.

PhotoHub envoie la configuration au serveur (`POST /api/v1/sources`). Un clic,
pas de terminal. La configuration n'est déclarée réussie que si le montage CIFS
est **réellement** en place, lisible, persisté, et visible dans l'API après
rechargement.

## Topologie validée (août 2026)

Les services restent sur le Raspberry Pi. Les postes Windows n'ont que PhotoHub.

```text
ASUS-DEV
└── PhotoHub uniquement
        │
        │ SMB
        ▼
      pi4dev
      ├── morfPhoto
      ├── morfAnalytics
      └── autres services morfSystem
        ▲
        │ SMB
        │
PC-FRED / Legion
└── PhotoHub uniquement
```

Le test réel a montré simultanément, dans le même PhotoHub, des dossiers des deux
machines :

```text
/mnt/photos_asus-dev/Archives/2003/2003-06-07
/mnt/photos_asus-dev/Archives/2004/2004-01-01
/mnt/photos_pc-fred/Photos/2026/2026-08-10
```

## Règle

**Une machine source = un hostname = un partage SMB = un point de montage = un
fichier de credentials = une racine morfPhoto.**

Le hostname Windows est l'identifiant canonique. Il est normalisé de façon
déterministe (`ASUS-DEV` → `asus-dev`, `PC-FRED` → `pc-fred`) et sert partout
pareil. L'adresse IP sert uniquement à joindre le partage, jamais à le nommer.

```text
ASUS-DEV
├── partage Windows : celui créé par l'assistant
├── point de montage : /mnt/photos_asus-dev
├── credentials : /etc/morfsystem/smb-photos-asus-dev.cred
└── root morfPhoto : /mnt/photos_asus-dev

PC-FRED
├── point de montage : /mnt/photos_pc-fred
├── credentials : /etc/morfsystem/smb-photos-pc-fred.cred
└── root morfPhoto : /mnt/photos_pc-fred
```

Les deux sources sont complètement séparées côté serveur, tout en étant
exploitées par **la même** instance de morfPhoto. Configurer une machine ne doit
jamais écraser ni modifier celle d'une autre.

Il n'existe plus de ressource générique partagée (`/mnt/photos`,
`/etc/morfsystem/smb-photos.cred`). Les anciennes racines déjà en place
(`/mnt/photos`, `/mnt/photos_asus`, `/srv/archives-photo`…) sont **conservées**.
Pas de migration destructive automatique.

## Déroulement

Depuis PhotoHub, sur le poste qui détient les photos :

1. Partager le dossier (bouton « Créer le partage », en administrateur).
2. Saisir le **nom d'utilisateur Windows de cette machine** et le mot de passe
   adapté au type de compte (voir ci-dessous).
3. Cliquer sur **« Envoyer la config au serveur morfPhoto »**.

Le serveur, dans l'ordre :

1. identifie et normalise le hostname ;
2. crée `/mnt/photos_<slug>` (idempotent) et vérifie qu'il existe ;
3. écrit `/etc/morfsystem/smb-photos-<slug>.cred` (`0600`, `root:root`) ;
4. teste le montage CIFS **sans** `nofail` ;
5. vérifie dans `/proc/mounts` qu'il s'agit bien d'un CIFS lisible ;
6. n'écrit `/etc/fstab` qu'après cette validation (idempotent par point de
   montage, `nofail,x-systemd.automount`) ;
7. ajoute la racine dans `/etc/morfsystem/morfphoto/morfphoto.json` sans retirer
   les racines existantes, puis revalide le JSON ;
8. redémarre morfPhoto **seulement si** le JSON a changé ;
9. PhotoHub interroge `GET /status` et `GET /api/v1/roots` et refuse le succès
   tant que la nouvelle racine n'y figure pas.

Si le montage échoue, la racine n'est **pas** ajoutée au JSON.

Relancer l'envoi depuis la même machine est idempotent : pas de doublon fstab,
pas de second fichier `.cred`, pas de racine dupliquée, pas de redémarrage si
rien n'a changé.

Une fois cette opération terminée, il n'y a plus rien à faire à la main sur le
Pi : on sélectionne les dossiers dans PhotoHub et on indexe.

## Authentification Windows (validée en conditions réelles)

Windows Hello (PIN, empreinte, visage) et les passkeys **ne sont pas** un mot de
passe SMB. Une machine peut fonctionner des mois avec un PIN sans que l'on
saisisse le vrai mot de passe du compte. C'est exactement ce que le partage
réseau refuse.

### Compte Windows local

```text
username = nom d'utilisateur Windows de la machine
password = mot de passe de session Windows (compte local)
```

Exemple : `ASUS-DEV\Fred` → `username=Fred`, mot de passe de la session locale.

### Compte Windows lié à un compte Microsoft

Le cas testé (ASUS) est contre-intuitif. **L'adresse e-mail n'est pas
l'identifiant SMB.**

```text
username = nom d'utilisateur Windows de la machine
password = mot de passe du compte Microsoft associé
```

Exemple :

```text
Compte visible sur la machine : ASUS-DEV\Fred
Compte Microsoft associé     : adresse@example.com

Credentials SMB :
username=Fred
password=mot_de_passe_du_compte_Microsoft
```

### Ce qu'affiche PhotoHub

L'assistant propose les deux situations, avec le même champ « nom
d'utilisateur » (celui de Windows) et un mot de passe dont le libellé change.

Formulation courte :

> Compte local : utilisez votre mot de passe de session Windows.
> Compte Microsoft : utilisez le mot de passe de votre compte Microsoft.
> Dans les deux cas, utilisez comme identifiant le nom d'utilisateur Windows
> de cette machine, et non votre PIN Windows Hello.

## Problèmes rencontrés et solutions

Les messages de PhotoHub doivent suivre le **statut SMB** (`STATUS_…`) quand il
est présent dans la sortie de `mount.cifs`, pas seulement le code errno final.
`-13` (EACCES) est une conséquence, pas un diagnostic.

| Symptôme / statut | Ce que ce n'est pas | Ce que c'est | Que faire |
| --- | --- | --- | --- |
| `STATUS_LOGON_FAILURE` | Un montage CIFS « permissions » | Identifiant ou mot de passe faux | Vérifier le **nom Windows** (pas l'e-mail) et le vrai mot de passe (local ou Microsoft), jamais le PIN |
| `STATUS_ACCOUNT_LOCKED_OUT` puis `cifs_mount failed … -13` | « Authentification OK, permissions insuffisantes » | Compte Windows **verrouillé** (trop d'essais, stratégie de verrouillage) | Déverrouiller le compte sur le PC source, attendre la fin du lockout, réessayer |
| `NT_STATUS_ACCESS_DENIED` | Un mot de passe faux (souvent déjà `LOGON_FAILURE`) | Auth acceptée, le compte n'a pas le droit de lire **ce** partage | Droits du partage Windows (lecture pour ce compte) |
| `NT_STATUS_BAD_NETWORK_NAME` | Une auth | Partage inexistant ou nom faux | Recréer le partage depuis l'assistant, vérifier le nom |
| Hôte injoignable, timeout, *no route* | Une auth | Machine éteinte, mauvais réseau, pare-feu SMB | Allumer le PC, même LAN, autoriser SMB |
| `no match for /mnt/photos_<slug> found in /etc/fstab` | Un droit trop faible sur le partage | Helper setuid : `mount.cifs` regardait l'UID **réel** du service. Corrigé dans morfPhoto 0.9.5 (`setuid(0)` après mémorisation uid/gid du service) | Mettre à jour morfPhoto sur le Pi, renvoyer la config. Ne pas ajouter `fstab` à la main avant un montage validé |
| Barre d'indexation à 33 % / 66 % alors qu'un dossier est énorme | Un bug de copie | Ancienne progression = dossiers terminés / dossiers totaux | morfPhoto 0.9.6 : précomptage léger, `files_seen / files_total`. PhotoHub n'affiche un % que si `percent` n'est pas `null` |

### Compte verrouillé : message attendu

```text
Authentification SMB                    ✗
Compte Windows verrouillé

Le compte utilisé pour accéder au partage SMB est actuellement verrouillé.
Déverrouillez le compte Windows avant de réessayer.
```

Code helper : `account_locked`. PhotoHub affiche l'étape d'authentification en
échec, pas le montage CIFS comme seule cause.

## Progression d'indexation

`GET /api/v1/index/status` expose `progress.phase` (`discovering` puis
`indexing`), `files_seen`, `files_total` (ou `null`), `percent` (ou `null`),
et les dossiers comme information **secondaire**.

Pendant la découverte : barre indéterminée, « Recherche des fichiers… ».
Dès que le total de fichiers candidats est connu : `percent = files_seen /
files_total`. Trois dossiers de 10, 1000 et 20 fichiers donnent environ **1 %**
après le premier, pas 33 %.

Si le précomptage n'aboutit pas (source muette), `percent` reste `null` : pas
de faux pourcentage.

## Séparation des privilèges

Monter un partage cifs exige `root` ; le service morfPhoto tourne sans privilège.
Le seul point d'entrée privilégié est `morfphoto-helper` (setuid `4750`,
`/usr/lib/morfsystem/morfphoto/`). Le `postinst` du paquet pose aussi `750`
`root:<compte du service>` sur le **dossier** (sinon le binaire est invisible).
PhotoHub appelle `GET /api/v1/sources/ready` (verbe helper `probe`) avant
d'envoyer identifiants. Invariants durs : Linux, root, CIFS lecture seule, point de montage uniquement `/mnt/photos_<slug>`, mot de passe sur stdin,
un `.cred` par slug, JSON restauré si la génération est invalide.

## Les trois topologies

- **Serveur Linux — automatisé.** Cas nominal décrit ci-dessus (validé
  PhotoHub Windows × morfPhoto sur Pi).
- **Ce PC Windows.** morfPhoto et les photos sur la même machine : aucun partage.
  Déclarer le dossier local dans `roots`.
- **Un autre PC Windows.** Racine UNC, pas de montage cifs. Encore manuel.
