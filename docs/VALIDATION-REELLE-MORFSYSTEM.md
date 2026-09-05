# Validation réelle du fonctionnement de morfSystem

Document vivant, dernière mise à jour : **5 septembre 2026**.

Il ne remplace ni les [contrats](CONTRACTS.md), ni les [principes](ARCHITECTURE-PRINCIPLES.md), ni le [changelog](../CHANGELOG.md) de ce dépôt, ni le CHANGELOG de chaque projet. Il consigne ce qui a été **observé sur le parc réel**.

Documents liés : [Architecture](ARCHITECTURE.md) · [Écosystème](ECOSYSTEM.md) · [Filesystem](FILESYSTEM.md) · [Tester une mise à jour locale](TESTER-MISE-A-JOUR-LOCALE.md) · [Feuille de route](ROADMAP.md) · [FAQ](FAQ.md)

## État actuel - Écosystème validé

Dernière validation : **5 septembre 2026**.

🟢 **morfSystem est opérationnel de bout en bout en conditions réelles.**

Les services, applications et outils principaux sont installés et validés sur les
plateformes cibles : **Windows x64, Linux x64 et Raspberry Pi ARM64**. La chaîne
complète - développement, packaging, déploiement, synchronisation, supervision,
collecte et gestion de la photothèque - a été éprouvée sur le parc réel. Les paquets
ARM64 cross-compilés depuis WSL s'installent et tournent sur le Raspberry Pi ; la
supervision et le redémarrage des services sont éprouvés (dont la correction de
stabilité de morfMonitor 0.16.2, vérifiée sur `pi4dev`).

Côté photothèque, PhotoHub et morfPhoto sont opérationnels : découverte, indexation
incrémentale, aperçu des dossiers, tri et écran de qualification. La qualification
enregistre son contexte dans un sidecar `.morfphoto.json` écrit **à côté des photos**
(morfPhoto n'écrit que ce sidecar, jamais les photos). Le montage d'une source SMB en
lecture/écriture **par source** (morfPhoto 0.13.0 / PhotoHub 0.16.0) a été **validé sur
le parc** : après passage du montage CIFS en `rw` et redémarrage du Pi, un test réel de
création puis suppression de fichier a confirmé l'écriture, et un sidecar
`.morfphoto.json` est bien créé lors d'une qualification (voir le détail plus bas).

Statut : 🟢 **Écosystème opérationnel - première validation complète.**

« Opérationnel » ne veut pas dire « terminé » : le projet continue d'évoluer, mais son
socle est désormais assez mûr pour être utilisé et éprouvé en conditions réelles.

```text
                    MORFSYSTEM
                         │
          ┌──────────────┴──────────────┐
          │                             │
       BUILD                        DEPLOY
          │                             │
     Windows x64                  Windows x64
     Linux x64                    Linux x64
     Linux ARM64                  Linux ARM64
                                        │
                                        ▼
                                  Raspberry Pi
                                        │
                         ┌──────────────┼──────────────┐
                         │              │              │
                    Services       Supervision     PhotoHub
                         │                             │
                         ▼                             ▼
                    opérationnels                 morfPhoto
                                                       │
                                                       ▼
                                                Qualification
                                                       │
                                                       ▼
                                               .morfphoto.json
```

### Sources qualifiables : montage lecture/écriture validé (5 septembre 2026)

Le support des sources qualifiables a été rendu opérationnel de bout en bout, pour que
morfPhoto puisse écrire les sidecars `.morfphoto.json` **sans jamais modifier les fichiers
photo eux-mêmes**. Il faut distinguer nettement ce qui relève du **système** (le
comportement normal, pour toute installation) et ce qui a relevé d'une **migration**
ponctuelle d'une installation ancienne.

**Ce qui a été développé (comportement normal, installation neuve comprise).** Le système
sait désormais :

- déclarer une source comme `writable` (par défaut, une source reste en lecture seule) ;
- propager cette information de l'API à `PhotoModule`, puis au `SourceManager`, enfin au
  helper privilégié qui gère les montages ;
- demander un montage `rw` au lieu de `ro` ;
- détecter qu'un montage existant est dans le mauvais mode, et **le remonter** dans le
  mode demandé ;
- conserver ce mode dans la configuration du montage (`fstab`) ;
- permettre ensuite à morfPhoto d'écrire son `.morfphoto.json`.

Une installation neuve génère donc directement la configuration correspondant au modèle
`writable` : **aucune modification manuelle de `fstab` n'est requise.**

**Validation de migration d'une installation existante.** Sur `pi4fred`, la source SMB de
la photothèque était encore déclarée `ro` dans `/etc/fstab`, conformément à l'ancienne
configuration **antérieure** au support des sources qualifiables. Les droits Windows
étaient déjà compatibles avec l'écriture :

```text
SMB  : PC-FRED\fred -> CHANGE
NTFS : PC-FRED\fred -> FULL CONTROL
```

Cette configuration historique constituait donc le dernier verrou empêchant l'usage
effectif du mode `rw`. La source `photos_pc-fred` a été migrée en `rw` (remplacement de
`...,ro,...` par `...,rw,...`, uniquement pour la photothèque qualifiable ; les archives
peuvent rester en `ro`). Après redémarrage du Pi, le montage CIFS apparaît `type cifs
(rw,...)`, et un test réel de création puis suppression de fichier a confirmé l'écriture
sur le partage SMB, jusqu'à la création d'un `.morfphoto.json` lors d'une qualification.

Cette intervention est **spécifique à la migration de l'installation existante** et **ne
constitue pas une étape d'installation** : une installation neuve part directement du
modèle `writable`. Le point de fond : ce n'est pas Windows qui a été rendu writable (il
l'était déjà), c'est le **chemin complet jusqu'à la photothèque**, en levant le dernier
verrou hérité (le `ro` du montage CIFS dans `fstab`). `writable` n'est donc pas un simple
drapeau d'interface : il pilote réellement le mode de montage.

## Objet du document

Ce document rassemble les validations obtenues par l'usage réel de morfSystem. Il ne décrit pas seulement ce que l'architecture devrait permettre : il consigne ce qui a effectivement été compilé, installé, déployé, découvert, utilisé et confronté à plusieurs machines, systèmes et situations.

La démarche suivie est volontairement exigeante. Une compilation réussie n'est pas considérée comme une validation suffisante. Une fonction n'est tenue pour acquise qu'après avoir été confrontée au matériel, au réseau, aux différences de plateformes, à la persistance des données, aux redémarrages et, lorsque c'est pertinent, à plusieurs clients utilisant simultanément le même service.

## Environnement réellement éprouvé

Les validations ont été menées sur un parc hétérogène :

| Machine | Système et rôle | Validations principales |
| --- | --- | --- |
| `pi4fred` | Raspberry Pi 4 8 Go, production | Hébergement continu des services, indexations photo réelles, supervision du parc, écran et capteurs, collecte et analyse |
| `pi4dev` | Raspberry Pi 4, développement et test | Compilations ARM64, installations et mises à jour, essais avant production, découverte des services, hébergement temporaire isolé |
| Legion 5 | Windows 11, poste principal | Développement MinGW, compilation des applications, PhotoHub, configuration et pilotage de services distants |
| ASUS | Windows 10, environnement neuf | Reconstruction de la chaîne Qt, compilation depuis un poste nettoyé, validation de PhotoHub sans aucun service local |
| MacBook Pro | Linux Mint 22 | Compilation et exécution sous Linux x64, validation croisée de PhotoHub et de l'état distant |
| ESP32-S3 et ESP32-C3 | Matériel embarqué | Firmwares, capteurs, publication réseau, OTA et intégration au parc |

Cette diversité est importante : les résultats ne reposent ni sur une seule machine ni sur un environnement de développement unique qui masquerait ses propres dépendances.

## Comment lire ce document

Les chiffres cités (nombre de photos, températures, versions) sont des **observations datées**, pas des invariants. Une indexation en cours, un hôte différent (`pi4fred` vs `pi4dev`) ou un timeout trop court changent les compteurs. On ne mélange pas un total de 20 324 photos sur `pi4fred` avec un export de 2 641 fichiers sur `pi4dev`.

Ce n'est pas un journal de session quotidien. Les détails de chantier restent dans les CHANGELOG des projets concernés (SiteWatch, morfPhoto, morfAnalytics, morfCollector, etc.). Ici on ne retient que ce qui éclaire l'architecture.

### Composants réellement confrontés ici

PhotoHub, morfPhoto, morfAnalytics, morfBeacon, morfMonitor, SiteWatch, morfCollector, la chaîne Qt/CMake sur Windows et Linux, systemd et `morf doctor` sur Raspberry Pi, MeteoHub sur ESP32, les exigences de provenance de morfPackages.

### Hors de ce récit

Les validations absentes de ces pages ne sont pas des échecs : elles n'ont simplement pas été racontées ici. morfNotify, morfSync et morfDashboard ont désormais été **compilés, installés et démarrés** lors d'une primo-installation complète depuis zéro (voir « Primo-installation complète depuis zéro » plus bas), mais leur usage fonctionnel de bout en bout n'est pas entièrement raconté ici ; GatewayLab et ComponentHub ne le sont pas non plus. Voir [l'écosystème](ECOSYSTEM.md) pour la carte complète.

## Compilation sur les plateformes réellement utilisées

### Raspberry Pi ARM64

Les services C++ ont été compilés et exécutés sur Raspberry Pi. Les tests ARM64 ont révélé de vraies différences de compatibilité, notamment lors de la compilation de morfAnalytics 0.32.0 : la version de Qt disponible sur le Pi ne proposait pas `QJsonValue::toLongLong`. L'échec n'a pas été contourné ; le code a été corrigé, versionné en 0.32.1, recompilé et poussé.

Cette correction constitue une validation utile en elle-même : elle montre que la compatibilité ARM64 n'est pas déduite d'une compilation Windows, mais vérifiée avec la chaîne Qt réellement installée sur le Pi.

Les services installés ont aussi été confrontés à systemd, aux permissions, aux utilisateurs de service, aux répertoires de configuration et de données (voir [Filesystem](FILESYSTEM.md)), ainsi qu'aux ports réellement exposés. Le premier déploiement d'un jeton sécurisé a par exemple confirmé la copie du secret, tout en révélant une hypothèse erronée sur le compte systemd. La procédure a été corrigée en interrogeant l'unité avec `systemctl show` et en appliquant les permissions minimales au compte effectivement utilisé. Le déroulé pas à pas d'une mise à jour depuis morfMonitor est dans [Tester une mise à jour locale](TESTER-MISE-A-JOUR-LOCALE.md).

### Windows 11 avec MSYS2/MinGW

Le Legion constitue l'environnement Windows principal historique. La chaîne CMake, Ninja, GCC/G++ et Qt 6 y a compilé les applications et outils du système. Les compilations MinGW isolées de morfUpdate et morfMonitor ont notamment été validées après correction des dépendances et des procédures de déploiement.

PhotoHub y fonctionne comme client réel de morfPhoto. Il ne se contente pas d'afficher une interface locale : il découvre le service distant, récupère sa configuration, lance des opérations et reflète leur progression.

Un smoke test de PhotoHub a également révélé qu'un simple dossier de build n'était pas autonome sous Windows. La distribution a été corrigée autour de `windeployqt` et du déploiement MinGW. Là encore, la validation a porté sur l'exécutable utilisable, pas uniquement sur la cible compilée.

### Windows 10 dans un environnement neuf

L'ASUS a servi de test de reconstruction et non de prolongement silencieux du Legion. Qt 6.11.1 y a été installé dans un environnement officiel Qt, avec ses propres outils MinGW, Ninja et CMake. Les problèmes de preset, de chemins, de cache CMake et de dépendances ont été rencontrés et consignés au lieu d'être masqués par les habitudes du poste principal.

PhotoHub a ensuite été compilé et lancé sur cet ASUS après nettoyage des services locaux. Au moment du test décisif, aucun service morfSystem ne tournait sur l'ASUS : seule l'application PhotoHub était présente. Cela a éliminé l'hypothèse qu'un composant local non identifié puisse rendre le test artificiellement positif.

### Linux x64

Le MacBook sous Linux Mint 22 a fourni un troisième environnement pour PhotoHub, après Windows et le Raspberry Pi. L'application y a été compilée et exécutée réellement. Elle s'est annoncée par morfBeacon, a été découverte par morfMonitor comme application non supervisée et a retrouvé l'état central détenu par morfPhoto sur `pi4fred`.

Depuis Linux, PhotoHub a retrouvé 20 324 photos, 11 dossiers actifs, leurs chemins et leurs racines, notamment `/mnt/photos` et `/srv/archives-photo`. Cet état avait été construit depuis Windows. Il n'y avait pourtant aucune synchronisation directe entre les deux applications clientes.

Le test a ainsi confirmé qu'un client Windows et un client Linux pouvaient observer et piloter le même service sans posséder chacun une copie privée de sa configuration.

### ESP32 et périphériques

MeteoHub a été exécuté sur ESP32-S3 avec ses capteurs AHT20, BMP280 et LD2410C, son historique, son cache sur carte SD, son annonce mDNS et les mises à jour OTA. Les données produites sont consommées par le reste de l'écosystème et par morfAnalytics.

Les essais matériels ont également permis de distinguer les contraintes réelles des suppositions : capteurs présents ou absents, périphériques série, affichage, alimentation et comportement au redémarrage. Le matériel embarqué n'est donc pas représenté uniquement par des simulateurs.

## Déploiement, installation et maintenance

### Déploiements sur les Raspberry Pi

Les services ont été construits, installés et relancés sur `pi4dev` puis sur `pi4fred`. Les validations ont porté sur :

- la création et la mise à jour des unités systemd ;
- la séparation entre binaires, configuration et données ([Filesystem](FILESYSTEM.md)) ;
- la conservation de la configuration personnelle lors des mises à jour ;
- la vérification des ports et des URL d'état ;
- la détection des services après leur démarrage ;
- la supervision des services déclarés comme attendus ;
- le comportement après redémarrage des machines.

Les métadonnées héritées du projet modèle ont réellement produit des incohérences : `template: true`, URL d'état restée sur le port 8901 au lieu de 8793 et descriptions systemd inadaptées. Ces erreurs ont été corrigées dans morfPhoto, puis la resynchronisation des composants vendorés a été vérifiée par `morf doctor`.

La mise à jour de morfBeacon a été propagée dans plusieurs projets, avec contrôle des versions intégrées. Ce test a confirmé l'intérêt d'un composant partagé tout en montrant qu'une synchronisation doit rester observable et vérifiable.

### Installation neuve Windows

L'ASUS a aussi servi à éprouver ce qu'une documentation d'installation neuve doit réellement contenir. Les problèmes rencontrés ont porté sur la chaîne Qt, les presets CMake, les variables d'environnement, la disponibilité de `nlohmann_json`, les caches de build et la différence entre une configuration personnelle fonctionnelle et un exemple distribuable.

Cette validation a conduit à une règle ferme : les dépôts et paquets ne doivent pas embarquer les adresses, noms de machines, chemins ou identifiants propres au parc de Fred. Une installation doit proposer des exemples génériques ; la configuration personnelle est créée et conservée séparément.

### Primo-installation complète depuis zéro (pi4dev, 23 août 2026)

`pi4dev` a été remis à blanc (services, `/opt`, `/etc/morfsystem`, `/var/lib`, sources) puis reconstruit à partir d'un unique `git clone` de morfTools, suivi de `morf dev clone` (récupération de tout le parc) puis de `morf install --all`. Les neuf services déployables ont été **compilés en ARM64, installés en unités systemd et démarrés en une passe, à partir de rien** : morfUpdate, morfAnalytics, morfCollector, morfMonitor, morfNotify, morfPhoto, morfSensor, morfSync et morfDashboard.

Le test a confirmé plusieurs mécanismes en situation réelle :

- l'ordre de déploiement respecte une **priorité déclarée** (`deployPriority`) : morfUpdate passe avant morfMonitor, sans introduire de dépendance entre services (morfMonitor tolère l'absence de morfUpdate) ;
- les dépendances système déclarées sont résolues sur la machine réelle : Qt SerialPort présent a réactivé le driver LD2410C de morfSensor ; exiftool reste le prérequis runtime de morfPhoto ;
- le mode `config replace` a posé chaque configuration avec sauvegarde préalable ;
- les prérequis de compilation (toolchain, Qt 6, exiftool) documentés dans le guide de développement se sont révélés suffisants et exacts.

Comme pour les autres validations, la primo-installation a surtout eu de la valeur par les **défauts qu'elle a révélés**, chacun corrigé puis reconfronté à la situation :

1. morfDashboard échouait à l'étape de configuration : son `service.py` propre n'exposait pas le verbe `config` appelé sur chaque projet lors d'un déploiement `replace`. Ajouté (délégué à son propre script d'installation) ; l'installation complète passe désormais à 9 services sur 9.
2. morfMonitor affichait morfUpdate « introuvable » dans la vue Écosystème alors qu'il tournait : morfUpdate était déclaré dans `beacon_apps` sans émettre de heartbeat morfBeacon. Retiré de cette liste — il reste supervisé en local par systemd et `/status`.
3. un mot de passe SMB erroné remontait un « permission denied » générique, l'étape d'authentification étant marquée réussie à tort : `mount.cifs` n'expose pas le motif NT, seul le journal noyau le contient. Le helper privilégié de morfPhoto lit désormais `dmesg` et distingue un échec d'authentification (`STATUS_LOGON_FAILURE`) d'un refus de permission (`STATUS_ACCESS_DENIED`).
4. les étapes de déploiement privilégiées laissaient un cache bytecode Python `root:root` dans l'arbre source de l'utilisateur, bloquant une remise à blanc lancée sans privilège. Les exécutions Python élevées tournent maintenant sans écrire ce cache, l'arbre source reste entièrement à l'utilisateur.

Cette validation renforce la ligne « installations et mises à jour » de `pi4dev` : le parc entier se reconstruit depuis un dépôt unique et un environnement nu, dans un ordre prévisible, sans configuration manuelle préalable entre services.

### Packaging et traçabilité

Le travail sur morfPackages a défini et éprouvé les exigences nécessaires à une distribution fiable : synchronisation Git avant production, artefacts propres à la plateforme courante, provenance dans `build-info.json`, version source, commit, état dirty, architecture, preset et date UTC.

Le principe retenu est que deux fichiers portant le même nom ne sont pas réputés équivalents sans contrôle de provenance et de checksum. Une construction `--no-build` doit valider strictement l'artefact existant au lieu de supposer qu'il correspond à la version annoncée.

## Validation de la découverte et du modèle distribué

### Découverte réseau réelle

morfBeacon a été vérifié entre plusieurs machines physiques, selon le [contrat de découverte](CONTRACTS.md) (capacité, jamais le nom). Des applications et services lancés sur Windows, Linux et Raspberry Pi ont été annoncés, découverts et distingués selon leur rôle. morfMonitor a ainsi pu différencier un service administrable d'une application cliente visible mais non supervisée.

La découverte ne se limite pas à l'apparition d'un nom dans une liste : elle a été utilisée pour sélectionner un service, ouvrir son interface, lui envoyer une configuration et déclencher des traitements.

### Validation croisée PhotoHub sur Windows et Linux

Un premier test croisé avait déjà montré que PhotoHub sous Linux retrouvait exactement l'état de morfPhoto construit depuis Windows. Les 20 324 photos et les 11 dossiers actifs étaient servis par `pi4fred`, et non transférés ou resynchronisés entre les postes clients.

Ce résultat validait le [huitième principe](ARCHITECTURE-PRINCIPLES.md) : le service possède l'état ; les clients le consultent et le modifient par son API.

### Validation croisée Legion, ASUS et pi4dev

Le test du 21 août 2026 pousse cette validation plus loin.

Seul `pi4dev` a été laissé actif comme hôte de service. Une indexation de photos locales au Legion a été lancée depuis PhotoHub sur le Legion. PhotoHub a ensuite été compilé et lancé sur l'ASUS nettoyé, sans aucun service morfSystem local.

Après découverte de morfPhoto sur `pi4dev`, PhotoHub sur l'ASUS a immédiatement affiché les dossiers déjà configurés et leur état d'avancement. Depuis cette seconde instance, des dossiers locaux à l'ASUS ont été ajoutés à l'indexation. Presque instantanément, PhotoHub sur le Legion a affiché ces nouveaux dossiers comme configurés.

Ce test valide plusieurs propriétés en une seule situation réelle :

1. PhotoHub est un client interchangeable et ne dépend pas de la machine qui l'a compilé en premier.
2. morfPhoto est l'unique source de vérité pour la configuration et l'état de l'indexation.
3. Deux PhotoHub n'ont besoin d'aucune synchronisation directe.
4. Les dossiers peuvent rester sur les machines qui les possèdent ; il n'est pas nécessaire de les transférer vers le Legion pour les déclarer.
5. Un client nouvellement compilé retrouve immédiatement l'état existant après découverte du service.
6. Une modification effectuée depuis un client devient visible depuis l'autre par l'intermédiaire du service central.
7. L'absence totale de service sur l'ASUS écarte la possibilité d'une réussite due à un état local résiduel.

Le flux réellement observé est donc :

```text
Photos du Legion ─┐
                  ├──> morfPhoto sur pi4dev <──> PhotoHub sur le Legion
Photos de l'ASUS ─┘                         └──> PhotoHub sur l'ASUS
```

Il ne s'agit pas d'une synchronisation de fichiers entre PC. Chaque poste conserve ses images ; morfPhoto centralise la connaissance des dossiers et de l'avancement ; chaque PhotoHub reflète le même état.

## Validation de la chaîne photo complète

La chaîne PhotoHub → morfPhoto → extraction EXIF → SQLite → morfAnalytics a été testée de bout en bout.

Une première indexation réelle d'environ 23 000 photos a été menée sur `pi4fred`. Pendant cette opération, le Raspberry Pi est resté à environ 44 °C avec une charge processeur faible. Ce résultat confirme l'objectif d'une indexation progressive qui ne monopolise pas la machine et ne rend pas l'environnement incontrôlable.

Les tests ont couvert des dossiers sur stockage local, des montages réseau et des archives anciennes issues de CD ou de supports externes. Ils ont aussi confirmé l'intérêt de conserver les métadonnées EXIF brutes et de laisser morfAnalytics interpréter les usages, plutôt que de réécrire l'histoire photographique dans la base d'indexation.

Les contrôles du 21 août ont révélé une divergence réelle entre plusieurs vues : 2 833 éléments dans une liste, 2 474 dans un résumé, environ 2 641 dans un export de dataset et 2 306 dans l'instantané analytics obtenu avant expiration d'un délai de huit secondes. Le délai d'analyse a été porté à 60 secondes et un tableau de cohérence a été ajouté dans la configuration.

Le problème n'a pas été maquillé dans morfAnalytics : les écarts entre liste, résumé et dataset restent identifiés comme un travail à mener dans les API de morfPhoto. Le document de validation conserve donc aussi les limites connues. Une indexation encore en cours sur `pi4dev` a d'ailleurs faussé des comparaisons trop rapides : attendre la fin du scan avant de conclure.

## Validation de la collecte et de l'analyse distribuées

### SiteWatch, GitHub et morfCollector

Le fonctionnement GitHub a été éprouvé avec des dépôts réellement sélectionnés, des collectes immédiates, des archives quotidiennes et des instances présentes sur plusieurs hôtes.

Les essais ont permis de corriger un modèle initialement inversé. Le modèle effectivement retenu et testé est :

```text
GitHub → SiteWatch → morfAnalytics
GitHub → morfCollector → SiteWatch → morfAnalytics
```

SiteWatch interroge GitHub à la demande, conserve et consolide les métriques, puis publie sa vérité. morfCollector archive les réponses brutes et fournit seulement les observations pouvant combler une période manquante. morfAnalytics analyse exclusivement les données consolidées par SiteWatch ; il n'arbitre jamais entre deux sources concurrentes.

Les essais réels ont révélé puis permis de corriger :

- l'envoi nécessaire de la configuration après une modification locale ;
- l'apparition indésirable des dépôts décochés dans les statistiques ;
- l'absence de publication automatique lors de la découverte de morfAnalytics ;
- une connexion SQLite fermée à la sortie de sa portée ;
- un HTTP 405 dû à une route ou une instance incorrecte ;
- la sélection involontaire du premier morfAnalytics ou morfCollector découvert, souvent sur le mauvais Pi ;
- la nécessité d'un choix automatique ou manuel cohérent des instances.

Les corrections ont été testées en usage réel, notamment avec `pi4fred` et `pi4dev` simultanément visibles. La présence de plusieurs services identiques dans le parc n'est donc plus un cas théorique. SiteWatch 1.18.0 (choix du collecteur et de morfAnalytics) et morfAnalytics 0.34.1 (un poste Photo = un nom d'hôte) en sont les versions constatées sur le parc au 21 août 2026.

### Supervision et état matériel

morfMonitor a été confronté aux services présents, absents, attendus ou non déclarés. La distinction entre `present`, `none` et `degraded`, ainsi qu'entre matériel attendu et matériel effectivement détecté, vient d'observations réelles du parc.

Le redémarrage de `pi4fred`, les alertes visibles sur l'écran de `pi4dev`, les gels ponctuels, les services absents et les périphériques non détectés ont servi de situations de test. Le système a également validé l'annonce d'une application comme PhotoHub sans la traiter comme un service systemd à administrer.

## Validation par les incidents et les redémarrages

Une part importante de la solidité actuelle vient des échecs rencontrés :

- une connexion SQLite disparaissait après l'ouverture de l'écran GitHub ;
- un timeout trop court produisait un instantané photo incomplet ;
- la première instance découverte n'était pas nécessairement la bonne ;
- un port hérité du modèle envoyait vers le mauvais service ;
- une méthode Qt disponible sur une plateforme ne compilait pas sur le Pi ;
- un dossier de build Windows n'était pas distribuable tel quel ;
- une hypothèse sur l'utilisateur systemd rendait une installation incorrecte ;
- la mise en veille profonde d'un PC pouvait perturber un montage réseau utilisé par le Pi ;
- un service permanent oublié faussait l'interprétation de la charge et du comportement de la machine.

Ces incidents ne diminuent pas la validation. Ils en constituent une partie essentielle, car chaque correction a été confrontée à la situation qui avait révélé le défaut.

## Ce que les essais permettent d'affirmer

À ce stade, les validations réelles permettent d'affirmer que :

- morfSystem fonctionne sur un parc hétérogène et non sur une machine unique ;
- les composants principaux sont compilés dans leurs environnements cibles réels ;
- les services Raspberry Pi peuvent être installés, démarrés, supervisés et mis à jour ;
- les applications clientes Windows et Linux découvrent les services et retrouvent leur état ;
- plusieurs clients peuvent piloter le même service sans synchronisation directe ;
- les données et configurations restent la responsabilité du service désigné ;
- un poste client ne doit pas nécessairement héberger les services ni copier les fichiers d'un autre poste ;
- les redémarrages, absences de services, différences de Qt, permissions systemd et multiples instances ont été réellement rencontrés ;
- les incohérences observées sont rendues visibles et attribuées au bon composant au lieu d'être compensées silencieusement ailleurs ;
- la structure « un service, une responsabilité » tient face aux usages réels.

## Limites conservées explicitement

Ce document ne prétend pas que tout est définitivement validé. Certaines vérifications restent locales à une plateforme ou à un projet. Les écarts entre les différentes API de comptage de morfPhoto ne sont pas encore résolus. Toutes les combinaisons de services, architectures et modes de déploiement ne sont pas nécessairement couvertes par un même scénario automatisé.

La portée de la validation est donc précise : les scénarios décrits ici ont été réellement exécutés et observés. Ils apportent une preuve solide du fonctionnement de l'architecture, de la portabilité des principaux composants et de la cohérence du modèle distribué. Ils ne remplacent pas les futurs essais ; ils constituent une base factuelle sur laquelle ceux-ci pourront s'appuyer.

## Conclusion

morfSystem n'est plus seulement une architecture cohérente sur le papier. Il a été compilé sur plusieurs plateformes, installé sur plusieurs générations d'environnements, déployé sur des Raspberry Pi, utilisé depuis Windows et Linux, raccordé à de vrais capteurs et confronté à des dizaines de milliers de photos, à plusieurs clients simultanés, à des redémarrages, à des erreurs de configuration et à des services concurrents sur le même réseau.

Le test croisé entre le Legion, l'ASUS et `pi4dev` résume particulièrement bien cette maturité : un poste propre, ne possédant que PhotoHub, découvre le service central, retrouve immédiatement le travail lancé depuis un autre poste, ajoute ses propres dossiers locaux et rend cette modification visible presque instantanément sur le premier client. Aucune copie manuelle, aucune configuration parallèle et aucune synchronisation entre clients n'ont été nécessaires.

La validation repose donc moins sur l'absence d'erreurs que sur la capacité du système à révéler les erreurs, à préserver les responsabilités de chaque composant et à reproduire son fonctionnement sur des environnements différents. C'est cette confrontation répétée au réel qui permet aujourd'hui de considérer le socle de morfSystem comme effectivement fonctionnel.

## Tenir ce document à jour

Quand un scénario nouveau a été **vu sur le matériel**, on l'ajoute ici : date, machines, ce qui a été observé, ce qui reste ouvert. On ne recopie pas un CHANGELOG projet. Si le récit contredit un principe écrit dans [Architecture](ARCHITECTURE.md) ou les [règles de développement](DEVELOPMENT-RULES.md), c'est le principe ou le code qu'il faut revoir, pas ce journal.

## Voir aussi

- [Architecture](ARCHITECTURE.md) - la carte.
- [Principes d'architecture](ARCHITECTURE-PRINCIPLES.md) - les règles que ces essais éprouvent.
- [Contrats](CONTRACTS.md) - morfBeacon, HTTP, `/status`.
- [Filesystem](FILESYSTEM.md) - `/etc/morfsystem`, `/opt`, `/var/lib`.
- [Écosystème](ECOSYSTEM.md) - qui possède quelle donnée.
- [Tester une mise à jour locale](TESTER-MISE-A-JOUR-LOCALE.md) - un parcours d'update réel.
- [Feuille de route](ROADMAP.md) - ce qui reste à stabiliser.
