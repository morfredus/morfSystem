# morfSystem

> **Un écosystème de services autonomes, libres et interopérables, conçu pour fonctionner d'abord chez son utilisateur, pas chez son éditeur.**

[![Version](https://img.shields.io/badge/version-1.4.4-blue)](CHANGELOG.md)
[![Doc](https://img.shields.io/badge/type-documentation-informational)](docs/)
[![License](https://img.shields.io/badge/License-GPL--3.0--only-blue)](LICENSE)

*Lire dans une autre langue : [English](README.md) · **Français** (ce document).*

---

# Documentation

Ce dépôt constitue la documentation de référence de l'écosystème morfSystem.

Si vous découvrez le projet, nous vous recommandons de suivre l'ordre de lecture suivant :

## Découvrir

- 📖 [Bien démarrer](docs/GETTING-STARTED.md)
- 🚀 [Installer & compiler](https://github.com/morfredus/morfTools/blob/main/docs/GUIDE-DEMARRAGE.md) — le guide pratique : cloner, compiler, installer les services
- 🔄 [Tester une mise à jour locale](docs/TESTER-MISE-A-JOUR-LOCALE.md) — configurer morfUpdate et le bouton de morfMonitor pas à pas
- 📖 [Philosophie](docs/PHILOSOPHY.md) — *pourquoi* l'écosystème est conçu ainsi
- 📖 [Architecture](docs/ARCHITECTURE.md) — *comment* les pièces s'assemblent
- 📖 [Penser en morfSystem](docs/THINKING-IN-MORFSYSTEM.md)

## Comprendre

- 📖 [Principes d'architecture](docs/ARCHITECTURE-PRINCIPLES.md)
- 📖 [Les contrats](docs/CONTRACTS.md)
- 📖 [Décisions de conception](docs/DESIGN-DECISIONS.md)
- 📖 [Cadre de décision](docs/DECISION-FRAMEWORK.md)
- 📖 [Règles de développement](docs/DEVELOPMENT-RULES.md)
- 📖 [Gestion des données locales](docs/FILESYSTEM.md)
- 📖 [Gouvernance](docs/GOVERNANCE.md)
- 📖 [Cycle de vie des projets](docs/PROJECT-LIFECYCLE.md)

## Participer

- 📖 [Contribuer](docs/CONTRIBUTING.md) — comment participer, et les interdits
- 📖 [Checklist de création d'un projet](docs/checklists/NEW-PROJECT.md)

## Référence

- 📖 [Écosystème](docs/ECOSYSTEM.md)
- 📖 [Glossaire](docs/GLOSSARY.md) — les termes clés en un coup d'œil
- 📖 [Feuille de route](docs/ROADMAP.md)
- 📖 [Ce que morfSystem n'est pas](docs/NON-GOALS.md)
- 📖 [FAQ](docs/FAQ.md)

---

## Pourquoi morfSystem ?

morfSystem n'est pas né de la volonté de créer une nouvelle plateforme, un nouveau framework ou une nouvelle suite logicielle.

Il est né d'un constat.

Au fil des années, de nombreux projets personnels ont vu le jour : outils de supervision, capteurs connectés, applications de bureau, services réseau, utilitaires système...

Chaque projet répondait à un besoin précis.

Chaque projet fonctionnait.

Mais chacun réinventait une partie des mêmes mécanismes : découverte réseau, supervision, configuration, mises à jour, communication entre applications, déploiement...

La solution la plus simple aurait été de tout fusionner dans une seule application.

Ce choix a volontairement été rejeté.

À l'inverse, la tendance actuelle consiste souvent à découper les applications en une multitude de microservices fortement dépendants les uns des autres, au point qu'aucun ne peut réellement fonctionner seul.

Ce choix a également été rejeté.

morfSystem est né entre ces deux extrêmes.

L'objectif n'est pas de construire une plateforme.

L'objectif est de construire un écosystème cohérent.

Chaque composant possède une responsabilité clairement définie.

Chaque composant reste utilisable indépendamment.

Lorsqu'ils sont présents ensemble sur le même réseau, ils collaborent naturellement grâce à des contrats communs plutôt qu'à des dépendances imposées.

Cette différence est fondamentale.

morfSystem n'impose jamais un composant lorsqu'un autre est installé.

Il propose une coopération, jamais une obligation.

> Pour comprendre les motivations ayant conduit à cette architecture, consultez la **[Philosophie](docs/PHILOSOPHY.md)**.

---

# Ce que morfSystem est

morfSystem est un ensemble de logiciels libres partageant une même philosophie de conception.

Il ne s'agit pas d'une suite logicielle monolithique.

Il ne s'agit pas d'un système d'exploitation.

Il ne s'agit pas d'un framework.

Chaque projet possède son propre dépôt, son propre cycle de développement, sa propre documentation et sa propre responsabilité.

Les composants partagent uniquement ce qui doit réellement être partagé :

- des conventions ;
- des contrats d'échange ;
- des protocoles documentés ;
- une philosophie commune.

Le reste appartient à chaque projet.

Cette approche permet à chaque composant d'évoluer sans remettre en cause l'ensemble de l'écosystème.

> L'organisation générale est présentée dans **[Architecture](docs/ARCHITECTURE.md)**.

---

# Ce que morfSystem n'est pas

Comprendre ce que morfSystem refuse est aussi important que comprendre ce qu'il propose.

morfSystem n'est pas un framework : il n'impose aucune manière de développer, seulement des principes.

Ce n'est pas une suite logicielle : on n'installe que les composants dont on a besoin.

Ce n'est pas un système d'exploitation : il respecte les différences entre plateformes plutôt que de les masquer.

Ce n'est pas *cloud first* : le fonctionnement nominal est local, l'accès distant reste une extension.

Ce n'est pas un orchestrateur : aucun composant central ne pilote les autres.

> Cette vision est développée dans **[Ce que morfSystem n'est pas](docs/NON-GOALS.md)**.

---

# Les principes fondateurs

## Une responsabilité par service

Chaque projet existe pour une seule raison, résumable en une phrase. Un nouveau besoin donne un nouveau projet, jamais un projet plus compliqué.

## L'autonomie avant tout

Un composant doit être utile même seul. La coopération avec les autres est une amélioration, jamais une condition de fonctionnement.

## Le réseau local est la vérité

Les services sont conçus pour fonctionner sur le réseau de leur utilisateur, sans aucune infrastructure distante. L'accès externe est une extension possible, jamais une obligation.

## Les contrats plutôt que les connaissances

Les composants collaborent par leurs interfaces publiques, jamais par leur fonctionnement interne. Une implémentation peut être réécrite ; le contrat, lui, reste stable.

## Les standards plutôt que la magie

Quand un standard ouvert répond au besoin (HTTP, JSON, UDP, fichiers texte), il est privilégié. Le recours à une technologie spécifique doit toujours être justifié.

## La simplicité avant l'effet de mode

On résout le problème avec le minimum de complexité nécessaire. Une solution éprouvée est souvent préférable à une solution récente mais plus complexe.

> Les principes sont détaillés dans **[Principes d'architecture](docs/ARCHITECTURE-PRINCIPLES.md)**.

---

# Une gouvernance commune

Bien que les composants restent indépendants, ils partagent un ensemble de conventions.

Une responsabilité clairement identifiée, une API publique lorsqu'un service communique, une découverte réseau via morfBeacon, des journaux exploitables, un numéro de version explicite, une licence libre : ces conventions évoluent peu et constituent la stabilité de l'écosystème.

Les outils communs, comme morfTools ou morfBeacon, facilitent cette cohérence sans devenir des dépendances obligatoires.

> Les conventions communes sont décrites dans **[Gouvernance](docs/GOVERNANCE.md)** et **[Règles de développement](docs/DEVELOPMENT-RULES.md)**.

---

# Une philosophie avant une technologie

morfSystem ne cherche pas à suivre les tendances.

Il cherche à construire un ensemble cohérent, robuste et compréhensible.

Les langages, les bibliothèques et les plateformes ne sont que des moyens.

Un composant se juge à sa responsabilité, jamais à sa pile technique.

C'est pourquoi une technologie n'est jamais adoptée simplement parce qu'elle est récente.

Avant d'ajouter une fonctionnalité, il cherche à préserver la simplicité.

C'est cette philosophie qui relie l'ensemble des projets.

Bien davantage que leur code.

---

# Pour aller plus loin

Le dépôt **morfSystem** décrit les principes communs de l'écosystème.

Chaque projet possède ensuite sa propre documentation dédiée.

Si vous souhaitez approfondir votre découverte, nous vous recommandons l'ordre de lecture suivant :

1. **[Bien démarrer](docs/GETTING-STARTED.md)**
2. **[Philosophie](docs/PHILOSOPHY.md)**
3. **[Architecture](docs/ARCHITECTURE.md)**
4. **[Principes d'architecture](docs/ARCHITECTURE-PRINCIPLES.md)**
5. **[Règles de développement](docs/DEVELOPMENT-RULES.md)**
6. **[Gouvernance](docs/GOVERNANCE.md)**
7. **[Écosystème](docs/ECOSYSTEM.md)**

Vous souhaitez **participer** ? Lisez **[Contribuer](docs/CONTRIBUTING.md)** : comment proposer un composant, les conventions à suivre et les interdits.
