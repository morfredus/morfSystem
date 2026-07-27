# morfSystem

> **Un écosystème de services autonomes, libres et interopérables, conçu pour fonctionner d'abord chez son utilisateur, pas chez son éditeur.**

---

# Documentation

Ce dépôt constitue la documentation de référence de l'écosystème morfSystem.

Si vous découvrez le projet, nous vous recommandons de suivre l'ordre de lecture suivant :

## Découvrir

- 📖 [Bien démarrer](docs/getting-started.md)
- 📖 [Philosophie](docs/philosophy.md)
- 📖 [Architecture](docs/architecture.md)

## Comprendre

- 📖 [Principes d'architecture](docs/architecture-principles.md)
- 📖 [Règles de développement](docs/development-rules.md)
- 📖 [Décisions de conception](docs/design-decisions.md)
- 📖 [Gouvernance](docs/governance.md)
- 📖 [Cycle de vie des projets](docs/project-lifecycle.md)

## Référence

- 📖 [Écosystème](docs/ecosystem.md)
- 📖 [Feuille de route](docs/roadmap.md)
- 📖 [Ce que morfSystem n'est pas](docs/non-goals.md)
- 📖 [FAQ](docs/faq.md)

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

> Pour comprendre les motivations ayant conduit à cette architecture, consultez la **[Philosophie](docs/philosophy.md)**.

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

> L'organisation générale est présentée dans **[Architecture](docs/architecture.md)**.

---

# Ce que morfSystem n'est pas

Comprendre ce que morfSystem refuse est aussi important que comprendre ce qu'il propose.

[...]

> Cette vision est développée dans **[Ce que morfSystem n'est pas](docs/non-goals.md)**.

---

# Les principes fondateurs

## Une responsabilité par service

[...]

## L'autonomie avant tout

[...]

## Le réseau local est la vérité

[...]

## Les contrats plutôt que les connaissances

[...]

## Les standards plutôt que la magie

[...]

## La simplicité avant l'effet de mode

[...]

> Les principes sont détaillés dans **[Principes d'architecture](docs/architecture-principles.md)**.

---

# Une gouvernance commune

Bien que les composants restent indépendants, ils partagent un ensemble de conventions.

[...]

Les outils communs, comme morfTools ou morfBeacon, facilitent cette cohérence sans devenir des dépendances obligatoires.

> Les conventions communes sont décrites dans **[Gouvernance](docs/governance.md)** et **[Règles de développement](docs/development-rules.md)**.

---

# Une philosophie avant une technologie

morfSystem ne cherche pas à suivre les tendances.

Il cherche à construire un ensemble cohérent, robuste et compréhensible.

[...]

Avant d'ajouter une fonctionnalité, il cherche à préserver la simplicité.

C'est cette philosophie qui relie l'ensemble des projets.

Bien davantage que leur code.

---

# Pour aller plus loin

Le dépôt **morfSystem** décrit les principes communs de l'écosystème.

Chaque projet possède ensuite sa propre documentation dédiée.

Si vous souhaitez approfondir votre découverte, nous vous recommandons l'ordre de lecture suivant :

1. **[Bien démarrer](docs/getting-started.md)**
2. **[Philosophie](docs/philosophy.md)**
3. **[Architecture](docs/architecture.md)**
4. **[Principes d'architecture](docs/architecture-principles.md)**
5. **[Règles de développement](docs/development-rules.md)**
6. **[Gouvernance](docs/governance.md)**
7. **[Écosystème](docs/ecosystem.md)**