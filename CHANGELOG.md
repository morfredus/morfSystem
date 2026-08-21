# Journal des versions - morfSystem

Le format s'inspire de [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/)
et du [versionnage sémantique](https://semver.org/lang/fr/).

morfSystem est un dépôt de **documentation** : ses versions décrivent l'évolution
de la doc de référence, pas d'un logiciel.

## [1.4.7] - 2026-08-21

### Ajouté

- Journal de [validation réelle](docs/VALIDATION-REELLE-MORFSYSTEM.md) : ce que le
  parc a réellement compilé, installé, découvert et confronté (plusieurs
  machines, PhotoHub croisé, GitHub, systemd). Relié depuis les README, la FAQ,
  l'architecture, l'écosystème et la feuille de route.

## [1.4.6] - 2026-08-21

### Modifié

- SiteWatch est la source de vérité des métriques GitHub (collecte immédiate et
  consolidation). morfCollector archive et rattrape. morfAnalytics n'analyse
  que cette vérité publiée.

## [1.4.5] - 2026-08-20

### Ajouté

- Les métriques d'audience des dépôts GitHub publics appartiennent à SiteWatch,
  pas à ComponentHub. morfCollector archive le brut (`github-traffic`) ;
  morfAnalytics GitHub en construit la mémoire. Documenté dans l'écosystème et
  la feuille de route.

## [1.4.4] - 2026-08-20

### Corrigé

- La notice de test de mise à jour locale décrit désormais le modèle LAN de
  confiance réellement livré : pas de jeton local ou GitHub pour les releases
  publiques, nettoyage des anciens fichiers de test et procédure fiable pour
  une installation depuis les sources.

## [1.4.3] - 2026-08-20

### Corrigé

- **Compte de service détecté au lieu d'être supposé.** Le guide de mise à jour
  locale récupère désormais l'identité réellement utilisée par l'unité
  `morfmonitor` avant de créer la copie du jeton. Il rétablit aussi les droits de
  lecture du fichier de configuration après `sudoedit`. Cela évite de bloquer le
  redémarrage lorsque l'installation utilise un compte différent de
  `morfmonitor`.

## [1.4.2] - 2026-08-20

### Ajouté

- **Guide complet de mise à jour locale.**
  [`docs/TESTER-MISE-A-JOUR-LOCALE.md`](docs/TESTER-MISE-A-JOUR-LOCALE.md)
  accompagne un débutant, depuis les paquets nécessaires jusqu'au premier clic
  dans morfMonitor : création du jeton GitHub limité, configuration réellement
  lue, permissions de chaque secret, cible explicitement autorisée, contrôles et
  diagnostic. Le périmètre local et les limites actuelles y sont explicités.

### Corrigé

- **Chemin de configuration unifié dans l'architecture.**
  `ARCHITECTURE.md` ne décrit plus l'ancien `/etc/<service>` : la configuration
  Linux est sous `/etc/morfsystem/<service>`, conformément à
  `FILESYSTEM.md` et aux paquets installés.

## [1.4.1] - 2026-08-19

### Modifié

- **Chemin d'installation rendu visible depuis le point d'entrée.** Le guide
  pratique (cloner, compiler, installer) existait mais n'était atteignable depuis
  aucune porte d'entrée de l'écosystème. `GETTING-STARTED.md` gagne une section
  « Je souhaite installer ou compiler morfSystem » qui pointe vers le guide de
  démarrage de morfTools, et le README (Discover / Découvrir) une entrée « Install
  & build / Installer & compiler ». Un débutant n'est plus laissé dans le
  conceptuel sans commande concrète.

## [1.4.0] - 2026-08-18

### Ajouté

- **Validation multi-plateforme du modèle « application cliente »** dans
  [`docs/ECOSYSTEM.md`](docs/ECOSYSTEM.md) (section PhotoHub). Le même PhotoHub
  tourne sous Windows et Linux x64, découvre morfPhoto par morfBeacon et retrouve
  l'état/config construit ailleurs (photothèque, dossiers, racines, index) : le
  client est sans état, rien à synchroniser entre deux exemplaires. Formalise
  aussi la distinction **application (non supervisée, vue mais non pilotée) vs
  service administrable** (ni `deploy`/`purge`/unité pour une application) et
  relie au septième principe (observer plutôt que contrôler).

## [1.3.1] - 2026-08-17

### Modifié

- **Doctrine d'effacement complétée** dans
  [`docs/FILESYSTEM.md`](docs/FILESYSTEM.md), au fil de l'implémentation : un
  emplacement configurable est **lu dans la configuration du service**, jamais
  deviné (sinon un effacement raterait une donnée déplacée) ; un effacement réel
  est **refusé tant que le service tourne** (éviter la corruption d'une donnée en
  cours d'écriture), sauf forçage explicite.

## [1.3.0] - 2026-08-17

### Ajouté

- **Doctrine d'effacement des données** dans
  [`docs/FILESYSTEM.md`](docs/FILESYSTEM.md) : le projet déclare les catégories
  qu'il sait effacer (identifiants libres, label, caractère destructif),
  l'orchestration se contente de transmettre l'intention. Prolonge la distinction
  `data` / `cache`, distingue effacement et désinstallation, et impose la
  simulation avant exécution. Le contrat technique vit dans morfDeploy.

## [1.2.1] - 2026-08-14

### Ajouté

- Complète l'intégration des trois nouveaux composants au parcours d'accueil
  [`docs/GETTING-STARTED.md`](docs/GETTING-STARTED.md) : morfPhoto (services),
  PhotoHub (applications), morfDeploy (outils).

## [1.2.0] - 2026-08-14

### Ajouté

- **Intégration des trois nouveaux composants dans la doc globale** :
  - **morfPhoto** (service métier) : indexation permanente de la photothèque,
    source de vérité des métadonnées photo ;
  - **PhotoHub** (application) : client de bureau de morfPhoto ;
  - **morfDeploy** (outils) : cœur de déploiement partagé, vendoré dans chaque
    service.
- Ajoutés au catalogue [`docs/ECOSYSTEM.md`](docs/ECOSYSTEM.md) (familles +
  diagramme des familles) et au registre des ports de
  [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) (morfPhoto `8793`, avec
  morfCollector `8792` complété au passage).

## [1.1.0] - 2026-07-28

### Modifié

- **Configuration regroupée sous `/etc/morfsystem/<service>`.** Tout le parc
  partage désormais un point d'entrée UNIQUE dans `/etc` (`/etc/morfsystem/`),
  qui contient le fichier partagé `morfsystem.json` et un sous-dossier par
  service, au lieu d'un `/etc/<service>` par service à la racine de `/etc`. Sous
  Windows : `%ProgramData%\morfsystem\<service>`. Les données restent sous
  `/opt/<service>`. L'ancien `/etc/<service>` est adopté à l'installation
  (`migrate_from`).


## [1.0.0] - 2026-07-27

### Ajouté

- **Documentation de référence complète de l'écosystème.** Parcours cohérent du
  *pourquoi* au *comment* : bien démarrer, philosophie, architecture (carte de
  l'écosystème, découverte, contrats, déploiement, exemple de bout en bout),
  principes d'architecture, contrats (avec un exemple concret morfBeacon),
  décisions de conception, cadre de décision, règles de développement, gestion des
  données locales, gouvernance, cycle de vie des projets, écosystème, glossaire.
- **Participer** : guide de contribution avec la section « Les interdits », et la
  checklist de création d'un projet.
- **Référence** : FAQ, feuille de route, « Ce que morfSystem n'est pas ».
- Convention de nommage des fichiers de doc en `MAJUSCULE.md`.
