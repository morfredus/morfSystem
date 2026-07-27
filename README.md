# morfSystem

> **Un écosystème de services autonomes, libres et interopérables, conçu pour fonctionner d'abord chez son utilisateur, pas chez son éditeur.**

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

---

# Ce que morfSystem n'est pas

Comprendre ce que morfSystem refuse est aussi important que comprendre ce qu'il propose.

morfSystem n'est pas une plateforme cloud.

Aucun composant ne dépend d'un service distant pour fonctionner.

Le réseau local constitue le fonctionnement nominal de l'écosystème.

L'accès distant peut exister, mais il reste une extension optionnelle.

---

morfSystem n'est pas une suite logicielle.

Installer un composant n'impose jamais l'installation des autres.

Chaque application doit conserver un intérêt lorsqu'elle est utilisée seule.

Les interactions entre composants enrichissent les fonctionnalités sans devenir une condition de fonctionnement.

---

morfSystem n'est pas un framework.

Les projets ne sont pas des modules d'un logiciel principal.

Ils sont des applications indépendantes partageant des conventions.

---

morfSystem n'est pas une collection de microservices dépendants.

La communication entre applications repose sur des contrats publics.

Une indisponibilité temporaire d'un service ne doit jamais provoquer l'arrêt des autres.

Les fonctionnalités complémentaires disparaissent.

Les fonctionnalités propres restent disponibles.

---

morfSystem n'est pas une démonstration technique.

Chaque choix architectural doit répondre à un besoin réel.

Une technologie n'est jamais intégrée parce qu'elle est moderne.

Elle est intégrée uniquement lorsqu'elle simplifie réellement l'écosystème.

---

# Les principes fondateurs

## Une responsabilité par service

Chaque projet possède une responsabilité clairement identifiée.

Lorsqu'un composant commence à remplir plusieurs rôles indépendants, il devient probablement deux projets.

Cette règle reste l'une des plus importantes de tout l'écosystème.

---

## L'autonomie avant tout

Chaque service doit pouvoir fonctionner seul.

L'absence d'un autre composant ne doit jamais empêcher son fonctionnement principal.

Les collaborations sont des enrichissements.

Jamais des dépendances.

---

## Le réseau local est la vérité

morfSystem est conçu pour fonctionner sur le réseau local.

Les services s'y découvrent automatiquement.

Les échanges sont directs.

Aucune infrastructure distante n'est nécessaire au fonctionnement normal.

---

## Les contrats plutôt que les connaissances

Les composants ne connaissent pas leurs voisins.

Ils connaissent uniquement des contrats.

Une API.

Un protocole.

Un format d'échange.

Cette approche permet de remplacer un composant sans modifier les autres.

---

## Les standards plutôt que la magie

Les échanges reposent autant que possible sur des protocoles simples et documentés.

HTTP.

JSON.

UDP.

Fichiers de configuration lisibles.

L'utilisateur doit pouvoir comprendre ce qui se passe sans avoir besoin d'un outil propriétaire.

---

## La simplicité avant l'effet de mode

La meilleure architecture n'est pas la plus complexe.

C'est celle qui restera compréhensible dans cinq ans.

Chaque nouvelle dépendance possède un coût.

Chaque nouvelle fonctionnalité possède une maintenance.

Chaque décision doit être justifiée.

---

# Une gouvernance commune

Bien que les composants restent indépendants, ils partagent un ensemble de conventions.

Ces conventions concernent notamment :

- la découverte réseau ;
- les ports réservés ;
- les API communes ;
- les informations publiées par chaque service ;
- les mécanismes de déploiement ;
- les conventions de configuration ;
- les règles de supervision.

Cette gouvernance n'est pas imposée par une application centrale.

Elle est définie par l'écosystème lui-même.

Les outils communs, comme morfTools ou morfBeacon, facilitent cette cohérence sans devenir des dépendances obligatoires.

---

# Une philosophie avant une technologie

morfSystem ne cherche pas à suivre les tendances.

Il cherche à construire un ensemble cohérent, robuste et compréhensible.

Chaque décision technique est prise au service de cette philosophie.

Jamais l'inverse.

Cette philosophie explique l'ensemble des choix réalisés dans chaque dépôt de l'écosystème.

Avant d'écrire du code, morfSystem cherche d'abord à définir les responsabilités.

Avant de créer une dépendance, il cherche à définir un contrat.

Avant d'ajouter une fonctionnalité, il cherche à préserver la simplicité.

C'est cette philosophie qui relie l'ensemble des projets.

Bien davantage que leur code.
