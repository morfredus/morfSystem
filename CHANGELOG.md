# Journal des versions - morfSystem

Le format s'inspire de [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/)
et du [versionnage sémantique](https://semver.org/lang/fr/).

morfSystem est un dépôt de **documentation** : ses versions décrivent l'évolution
de la doc de référence, pas d'un logiciel.

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
