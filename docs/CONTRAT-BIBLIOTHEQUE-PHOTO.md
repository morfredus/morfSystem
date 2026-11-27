# Contrat bibliothèque photo (`morfphoto-library/1`)

Ce document définit comment **morfPhoto** expose, en plus de l'EXIF des fichiers, la
**couche d'organisation et d'édition** que les logiciels photo posent à côté des images :
note, rejet, étiquette de couleur, mots-clés, titre, retouchée ou non, versions.

Il complète le contrat d'API de morfPhoto (`/api/v1`), le contexte photographique
(`morfphoto-context/2`) et la découverte par capacité (morfBeacon). Voir
[CONTRACTS.md](CONTRACTS.md).

**Statut : implémenté en partie par morfPhoto 0.19.0** (fournisseurs `darktable` et `xmp`,
XMP embarqué `file`, routes, filtres, facettes, export `with=library`). Le contrat a été posé
avant le code, comme l'exige la règle des contrats. La pagination par curseur et l'`ETag`/`304` (§7) sont livrés par
morfPhoto 0.20.0. Reste à faire : fournisseurs `rawtherapee`, `captureone`, `on1`. Il s'inspire de la bibliothèque EXIF + XMP du fork
`Rhettus/morfPhoto` (client Immage), mais s'en écarte sur trois points (voir §12).

---

## 1. Le besoin

morfPhoto indexe l'EXIF : il sait **comment** une photo a été prise. Il ne sait pas
**ce qu'on en a fait** : notée, rejetée, retouchée, étiquetée, rangée par mots-clés. Cette
information vit dans des fichiers à côté des images (les **sidecars**) ou dans le fichier
lui-même (XMP embarqué), et chaque logiciel a son format.

Ce contrat sert deux lecteurs :

- **PhotoHub** : dire quels logiciels ont laissé des traces et sur combien de photos.
- **morfAnalytics** : explorer la pratique (taux de retouche, notes, mots-clés, évolution
  des logiciels) avec le même système de filtres que l'EXIF.

## 2. Ce que le contrat n'est pas

- **Il n'écrit jamais.** morfPhoto lit les sidecars, ne les modifie, ne les crée ni ne les
  supprime. Le seul fichier que morfPhoto écrit à côté des photos reste `.morfphoto.json`.
- **Il ne fait pas de recherche sémantique** (CLIP, reconnaissance d'images). Hors périmètre ;
  si le besoin vient, ce sera un composant distinct.
- **Il ne remplace pas l'EXIF.** Il s'ajoute à l'export existant (`photos`, `dataset`), qui ne
  change pas.
- **Il n'expose pas le contenu brut des sidecars.** Pas de copie du XMP, des historiques de
  retouche ni des chemins de fichiers du serveur.

## 3. Souveraineté : qui fait autorité sur quoi

| Donnée | Autorité | Règle |
|---|---|---|
| Date de prise de vue, boîtier, objectif, focale, ouverture, ISO, vitesse | **Le fichier image (EXIF)** | Un sidecar ne les remplace jamais. |
| Position GPS | Le fichier, à défaut le sidecar | Le sidecar ne sert que si le fichier n'a pas de position ; la provenance est alors indiquée (`gps_source`). |
| Note, rejet, étiquette, mots-clés, titre, description, auteur, droits, nom de version, retouchée | **Les sidecars** (puis le XMP embarqué) | Voir la résolution au §5. |

Principe : ce qui décrit la **prise de vue** appartient à la photo et ne change pas selon le
logiciel ; ce qui décrit le **traitement** appartient au logiciel qui l'a posé.

## 4. Modèle commun

Une **photo** (un fichier image) porte une ou plusieurs **versions**. Une version est un
traitement distinct de la même image (copie virtuelle darktable, variante Capture One...).

### Valeurs inconnues

Une valeur **inconnue** est `null`, jamais une valeur par défaut. Une note absente n'est pas
une note de 0 ; une photo dont aucun logiciel ne dit si elle est retouchée n'est pas « non
retouchée ». C'est la règle des NULL préservés de l'export existant, étendue à cette couche.

### Version

| Champ | Type | Sens |
|---|---|---|
| `version` | entier | `0` = l'original, `1`, `2`... = copies. Numéro donné par le logiciel. |
| `is_copy` | booléen | `true` pour une copie. |
| `name` | string ou null | nom donné à la version par le logiciel. |
| `rating` | entier 0-5 ou null | étoiles. `0` = zéro étoile posé explicitement. |
| `rejected` | booléen ou null | rejetée. Indépendant de `rating`. |
| `picked` | booléen ou null | retenue (drapeau « pick »). Indépendant de `rejected`. *Livré par morfPhoto 0.23.0 ; ajout compatible, la version du contrat ne change pas.* |
| `color_labels` | liste de strings | parmi `red`, `yellow`, `green`, `blue`, `purple`. Vide si aucune. Liste de noms, pas de masque binaire. |
| `keywords` | liste de strings | un mot-clé par entrée ; les niveaux hiérarchiques sont séparés par ` \| ` (`Lieux \| France \| Bretagne`). |
| `title`, `description`, `creator`, `rights` | string ou null | champs descriptifs. |
| `edited` | booléen ou null | la version a été retouchée. |
| `sources` | objet | provenance de chaque valeur : `{"rating":"darktable","keywords":"xmp"}`. |

### Photo

| Champ | Type | Sens |
|---|---|---|
| `id` | entier | identifiant morfPhoto du fichier (celui de `/api/v1/photos`). |
| `path` | string | chemin du fichier, tel que déjà exposé par l'API. |
| `kind`, `extension` | string | `raw`, `jpeg`... et extension, comme l'export existant. |
| `taken_at`, `camera`, `lens`, `focal_length`, `aperture`, `iso`, `shutter_speed_s` | | EXIF, inchangé. |
| `gps` | objet ou null | `{latitude, longitude}`. |
| `gps_source` | string ou null | `file` ou l'identifiant du fournisseur qui a donné la position. |
| `versions` | liste | au moins une version si au moins un sidecar existe ; vide sinon. |
| `sidecars` | liste | `[{source, name}]` : logiciel et **nom** du fichier (jamais le chemin). |

## 5. Fournisseurs et résolution

Un **fournisseur** (provider) sait reconnaître et lire les sidecars d'un logiciel. Le contrat
ne dépend d'aucun logiciel : il définit l'interface, chaque fournisseur la remplit.

### Identifiants

| Identifiant | Logiciel / format | Remarque |
|---|---|---|
| `darktable` | darktable (`photo.ext.xmp`, `photo_NN.ext.xmp`) | versions numérotées. |
| `xmp` | XMP générique (Lightroom et Camera Raw, Bridge, digiKam, XnView, Photo Mechanic) | `photo.xmp` ou `photo.ext.xmp`. |
| `rawtherapee` | RawTherapee (`photo.ext.pp3`) | format INI. |
| `dxo` | DxO PhotoLab (`photo.ext.dop`) | tables imbriquées en texte. **Livré (morfPhoto 0.22.0)** : note, mots-clés, position, droits. « Retouchée » inconnue (PhotoLab écrit un `.dop` dès l'ouverture d'une image, avec corrections automatiques) ; copies virtuelles non lues. |
| `captureone` | Capture One (`.cos`, `.cot`) | format XML. |
| `on1` | ON1 | à la demande. |
| `lightcraft` | Lightcraft (`lc:flag` dans le même `.xmp` que darktable) | **Livré (morfPhoto 0.23.0, `reject` en 0.24.0)** : `pick` = retenue, `none` = aucun drapeau, `reject` = rejetée (les trois confirmés sur de vrais fichiers), le reste inconnu ; `lc:settings` non lu. Attribué à `lightcraft`, pas au logiciel qui a classé le fichier. |
| `file` | XMP **embarqué** dans l'image | toujours disponible, toujours en dernier. |

Les fournisseurs propriétaires (`rawtherapee`, `dxo`, `captureone`, `on1`) ne sont engagés
qu'après examen de **fichiers réels** : un parseur écrit d'après une documentation
incomplète produirait de fausses retouches.

### Configuration

Chaque fournisseur est activé un par un. Tout est **éteint par défaut** : sans configuration,
morfPhoto se comporte comme avant et `/api/v1/library` répond `404 library_disabled`.

```json
"sidecars": {
  "providers": ["darktable", "xmp"]
}
```

L'**ordre de la liste est l'ordre de priorité**. `file` est implicite et toujours en dernier.

### Résolution par champ

Plusieurs fournisseurs peuvent avoir un sidecar pour la même version (un `.xmp` darktable et
un `.pp3` RawTherapee). La résolution se fait **champ par champ** :

1. Pour un champ donné, le premier fournisseur de la liste dont la valeur n'est pas `null`
   l'emporte ; `sources` en garde la trace.
2. Un sidecar qui **connaît** un champ le fait valoir même quand il est vide : une liste de
   mots-clés vide dans un sidecar darktable signifie « tous retirés », et ne doit pas être
   recomplétée par les mots-clés embarqués dans l'image.
3. Exception pour `edited` **et `rejected`** : `true` si **au moins un** fournisseur le dit (un rejet fait dans un logiciel est un geste qu'un autre ne sait pas défaire) ; `false` si
   au moins un le dit et qu'aucun ne dit le contraire ; `null` sinon.

Les versions se rapprochent par **numéro** : le sidecar de la version 2 d'un fournisseur se
fusionne avec celui de la version 2 d'un autre. Un fournisseur sans notion de version ne
décrit que la version `0`.

### Ce que doit faire un fournisseur

- **Reconnaître** ses fichiers par nom, sans les ouvrir tous (une passe ne doit pas coûter une
  lecture par photo sur un montage réseau).
- **Rattacher** chaque sidecar à une image et à un numéro de version.
- **Lire** vers le modèle commun, valeurs inconnues en `null`.
- **Dire** si la version est retouchée, selon une règle documentée dans le fournisseur.
- **Ne jamais écrire**, ni créer de fichier.

## 6. Découverte

Quand la bibliothèque est activée, morfPhoto ajoute la capacité beacon **`photo_library`**
(en plus de `photo_index`). Un client découvre par capacité, jamais par nom.

`GET /api/v1/index/status` gagne un bloc `library` :

```json
"library": {
  "enabled": true,
  "proto": "morfphoto-library/1",
  "providers": ["darktable", "xmp", "file"],
  "photos_with_sidecar": 9120,
  "by_source": { "darktable": 8450, "xmp": 910, "file": 15720 }
}
```

`catalog_revision` (chaîne opaque, déjà utile aux clients qui gardent une copie) change dès
qu'une photo, une version ou un sidecar change.

## 7. Routes

Toutes sous `/api/v1`, `GET` seulement, derrière la clé d'API quand elle est configurée.

| Route | Rôle |
|---|---|
| `GET /library` | liste paginée de photos avec leurs versions, filtrable (voir §8). |
| `GET /library/{id}` | une photo. |
| `GET /library?ids=1,2,3` | plusieurs photos par identifiant (500 au plus). |
| `GET /library/facets` | comptes par valeur, chaque liste calculée avec **tous les filtres sauf le sien**. |
| `GET /library/subfolders?folder_path=` | sous-dossiers directs avec leur nombre de photos, sans parcourir tout le catalogue. |
| `GET /library/sidecars` | rapport : fournisseurs actifs, photos par fournisseur, photos sans sidecar, photos avec XMP embarqué (`with_embedded`), sidecars illisibles (`unreadable`). |

Pagination : `page`, `page_size` (500 au plus) ou `cursor` (pagination par curseur, stable
quand le catalogue change pendant la lecture) ; `skip_total=true` évite le comptage total.
`ETag` et `304` sont attendus sur la liste et les sous-dossiers. *Livrés par morfPhoto 0.20.0 : le curseur est le champ `next` d'une page, opaque, `null` sur
la dernière ; l'`ETag` est la révision du catalogue, renvoyée dans `If-None-Match`, et `/library`
ainsi que `photos/dataset` répondent `304` sans calcul.*

Erreurs : `404 library_disabled` (bibliothèque éteinte, `detail` indique l'option),
`400 bad_request` (filtre invalide, `detail` dit lequel), `401` (clé d'API).

## 8. Filtres

Un paramètre répété vaut « l'un de » (`keyword=a&keyword=b` : a **ou** b). Des paramètres
différents se combinent en « et ».

| Paramètre | Sens |
|---|---|
| `min_rating` | note minimale, 0 à 5. |
| `rejected` | `hide` (défaut), `only`, `all`. |
| `edited` | `edited`, `unedited`, `all`. |
| `picked` | `true`, `false` ; une photo passe si l'une de ses versions passe, une valeur inconnue ne passe jamais. |
| `color_label` | répétable ; `unlabeled=true` pour les photos sans étiquette. |
| `source` | répétable ; ne garde que les photos avec un sidecar de ce fournisseur. |
| `keyword` | répétable ; un mot-clé hiérarchique inclut ses descendants. |
| `kind`, `camera`, `lens`, `iso`, `aperture`, `focal`, `shutter` | répétables, valeurs de l'export EXIF. |
| `taken_from`, `taken_to` | `AAAA-MM-JJ`. |
| `folder_path`, `recursive` | dossier et sa descendance. |
| `filename` | sous-chaîne du nom de fichier. |

Les photos dont la valeur est `null` ne passent **pas** un filtre sur cette valeur (une photo
sans note ne passe pas `min_rating=3`), mais restent comptées à part dans les facettes
(`unknown`).

## 9. Export pour l'analyse

L'export compact `GET /api/v1/photos/dataset` **ne change pas**. Une option ajoute les
colonnes de la bibliothèque :

`GET /api/v1/photos/dataset?with=library`

- **Une ligne par photo**, jamais par version : les copies ne gonflent pas les comptages.
- La **version principale** est celle qui porte le plus petit numéro (normalement `0`).
- Colonnes ajoutées : `rating`, `rejected`, `edited` (version principale), `edited_any` et
  `rating_max` (toutes les versions), `versions` (nombre), `color_labels` (indices dans un
  dictionnaire), `source` (indice dans un dictionnaire des fournisseurs), `keywords` (liste
  d'indices dans un dictionnaire).
- Les `null` sont préservés, comme pour l'EXIF.
- Les dictionnaires `keyword`, `source` et `color_label` accompagnent les colonnes.

Sans `with=library`, ou bibliothèque éteinte, la réponse est exactement celle d'aujourd'hui.

`with` est une liste séparée par des virgules (`with=library,gps`). **`gps`** ajoute `latitude`, `longitude`
et `gps_source` (dictionnaire : `file`, ou le logiciel dont le sidecar a donné la position), selon la règle de
souveraineté du §3 : l'EXIF du fichier d'abord, le sidecar à défaut, `null` sans position. Il ne dépend pas de la
bibliothèque : l'EXIF seul suffit. *Livré par morfPhoto 0.21.0.*

## 10. Comportement attendu

- **Lecture seule**, y compris sur un partage monté en écriture.
- **Incrémental** : un sidecar modifié, ajouté ou supprimé relit **cette photo seulement**.
  Le changement d'un sidecar sans toucher à l'image est détecté (empreinte de taille et de
  date des sidecars).
- **Activation sans surprise** : activer un fournisseur déclenche une relecture unique à la
  passe suivante ; la désactiver retire ses valeurs sans toucher à l'EXIF.
- **Source injoignable** : une passe qui ne peut pas joindre une racine ne marque rien
  comme disparu ; les sidecars non relus gardent leur dernière valeur connue.
- **Sidecar illisible** : la photo reste indexée, le sidecar est compté dans
  `/library/sidecars` (`unreadable`), les autres sources continuent de servir.

## 11. Compatibilité

- **Additif.** `morfphoto-library/1` n'altère aucune route existante. Un client qui ne le
  connaît pas continue de fonctionner.
- **Évolution lente.** Ajouter un champ, un fournisseur ou un filtre ne change pas la
  version du contrat. Retirer ou changer le sens d'un champ passe à `/2`.
- **Un client ignore ce qu'il ne connaît pas** (champs et fournisseurs inconnus).

## 12. Écarts avec le fork Rhettus/morfPhoto

| Point | Fork | Ce contrat | Pourquoi |
|---|---|---|---|
| Étiquettes de couleur | masque binaire (1, 2, 4, 8, 16) | liste de noms | lisible sans table de correspondance, extensible. |
| Logiciels | darktable seul (nommage, hashes d'historique) | interface de fournisseurs | ne pas lier le contrat à un logiciel. |
| Contenu brut | `raw_xmp` disponible | absent | volumineux, peut révéler des chemins ou des historiques. |

Un client écrit pour le fork (Immage) ne peut donc pas lire ce contrat tel quel ; l'écart
est limité et documenté.

## 13. Consommateurs prévus

- **PhotoHub** : panneau des sidecars détectés (`/library/sidecars`), sans analyse.
- **morfAnalytics** : colonnes `with=library` dans le même système de filtres croisés que
  l'EXIF ; onglet « tri et retouche » ; croisement avec le contexte photographique. Les
  règles d'interprétation (« ce logiciel est mon outil principal », « cette note vaut
  sélection ») vivent dans la configuration de morfAnalytics, jamais dans morfPhoto.

## 14. Questions encore ouvertes

- Facettes de mots-clés hiérarchiques : **liste à plat** de chemins complets (`Lieux | France`),
  tranché à l'implémentation ; un filtre sur un mot-clé inclut ses descendants. Un arbre reste
  possible plus tard sans changer le contrat.
- Fournisseurs propriétaires : formats à vérifier sur fichiers réels avant tout parseur.
- Coût de la lecture d'un sidecar par photo sur un montage SMB : à mesurer sur la vraie
  photothèque avant de lever la limite de passe.
