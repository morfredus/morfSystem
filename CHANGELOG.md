# Journal des versions - morfSystem

Le format s'inspire de [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/)
et du [versionnage sémantique](https://semver.org/lang/fr/).

morfSystem est un dépôt de **documentation** : ses versions décrivent l'évolution
de la doc de référence, pas d'un logiciel.

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
