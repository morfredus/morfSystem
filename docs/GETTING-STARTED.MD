# Bien démarrer avec morfSystem

Bienvenue dans morfSystem.

Si vous découvrez l'écosystème, il peut paraître impressionnant au premier abord.

Plusieurs dépôts.

Plusieurs services.

Plusieurs applications.

Pourtant, son fonctionnement repose sur quelques idées simples.

Ce document a pour objectif de vous permettre de comprendre rapidement l'organisation générale avant de commencer à utiliser ou développer un composant.

---

# Comprendre avant d'installer

La première erreur consiste souvent à vouloir tout installer.

Ce n'est pas nécessaire.

morfSystem est constitué de projets indépendants.

Chaque dépôt possède sa propre documentation.

Chaque projet peut être utilisé seul.

L'objectif n'est donc pas d'installer tout l'écosystème.

L'objectif est d'installer uniquement les composants dont vous avez besoin.

---

# Comment est organisé l'écosystème ?

Les projets appartiennent généralement à quatre grandes familles.

## Les services

Ils exécutent une responsabilité précise.

Ils exposent généralement une API HTTP.

Ils peuvent annoncer leur présence sur le réseau grâce à morfBeacon.

Exemples :

- morfMonitor
- morfSync
- morfAnalytics
- morfNotify
- morfPhoto

---

## Les applications

Elles répondent à un besoin métier.

Elles peuvent utiliser un ou plusieurs services.

Elles restent cependant autonomes.

Exemples :

- MeteoHub
- GatewayLab
- ComponentHub
- SiteWatch
- PhotoHub

---

## Les composants embarqués

Ils fonctionnent sur des plateformes comme les ESP32.

Ils produisent ou collectent des informations.

Ils participent à l'écosystème en utilisant les mêmes contrats que les autres composants.

---

## Les outils

Ils facilitent le développement, le déploiement et l'administration.

Exemples :

- morfTools
- morfDeploy

---

# Par où commencer ?

Le meilleur point d'entrée dépend de votre objectif.

## Je souhaite installer ou compiler morfSystem

Le guide pratique, du premier clone jusqu'aux services qui tournent, est le
**guide de démarrage de morfTools** :
[GUIDE-DEMARRAGE.md](https://github.com/morfredus/morfTools/blob/main/docs/GUIDE-DEMARRAGE.md).

Il déroule tout dans l'ordre : prérequis, récupération des projets (`morf clone`),
vérification (`morf doctor`), compilation (`morf build`), installation des services,
configuration, et dépannage. Pour l'installation détaillée de l'environnement de
compilation sur une machine neuve, il renvoie à
[ENVIRONNEMENT-DEV.md](https://github.com/morfredus/morfTools/blob/main/docs/ENVIRONNEMENT-DEV.md).

Le guide de morfTools sert à installer et administrer les services. Il ne faut pas
pour autant tout installer : commencer par le ou les services réellement utiles,
puis ajouter le reste plus tard.

## Je souhaite essayer le bouton de mise à jour locale

Le bouton de morfMonitor délègue l'installation à morfUpdate, présent sur la
même machine. Il ne permet pas encore d'administrer une autre machine du réseau.

Suivre le guide pas à pas [Tester une mise à jour locale](TESTER-MISE-A-JOUR-LOCALE.md).
Il commence sur une installation déjà fonctionnelle et décrit la création du
jeton GitHub, les fichiers de configuration réellement lus, les permissions, les
contrôles et le dépannage.

## Je souhaite utiliser un projet existant

Rendez-vous directement dans le dépôt du projet concerné.

Chaque dépôt explique :

- son rôle ;
- son installation ;
- son utilisation.

---

## Je souhaite comprendre l'architecture

Commencez par lire :

- [Philosophie](PHILOSOPHY.md)
- [Architecture](ARCHITECTURE.md)
- [Principes d'architecture](ARCHITECTURE-PRINCIPLES.md)

Ils présentent les concepts fondamentaux.

---

## Je souhaite développer un nouveau composant

Nous vous recommandons ensuite de lire :

- [Contribuer](CONTRIBUTING.md)
- [Règles de développement](DEVELOPMENT-RULES.md)
- [Gouvernance](GOVERNANCE.md)
- [Cycle de vie des projets](PROJECT-LIFECYCLE.md)

Ces documents décrivent les conventions utilisées dans tout l'écosystème.

---

# Dois-je tout connaître ?

Non.

L'écosystème est conçu pour être découvert progressivement.

Il n'est pas nécessaire de comprendre l'ensemble de l'architecture avant de commencer à utiliser un composant.

Les concepts deviennent plus naturels à mesure que l'on découvre les différents projets.

---

# Où trouver la documentation technique ?

Ce dépôt décrit l'architecture de morfSystem.

La documentation technique de chaque composant se trouve dans son propre dépôt.

Cette séparation est volontaire.

Elle permet à chaque projet d'évoluer indépendamment tout en conservant une vision commune de l'écosystème.

---

# Une dernière recommandation

Lorsque vous découvrez morfSystem, ne cherchez pas immédiatement à comprendre tous les composants.

Choisissez-en un.

Comprenez sa responsabilité.

Observez comment il communique avec les autres.

Vous découvrirez progressivement que l'écosystème ne repose pas sur la complexité de chacun de ses projets, mais sur la simplicité de leurs interactions.
