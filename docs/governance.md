# Gouvernance de l'écosystème

morfSystem est composé de projets indépendants.

Cette indépendance ne signifie pas que chacun évolue selon ses propres règles.

L'écosystème repose sur un ensemble de conventions communes qui garantissent sa cohérence dans le temps.

Ces conventions constituent la gouvernance de morfSystem.

---

# La gouvernance avant les outils

Aucun outil ne définit les règles de morfSystem.

Les règles existent indépendamment de leur implémentation.

Les outils ne font que les appliquer, les vérifier ou les simplifier.

Cette distinction est importante.

Un composant ne devient jamais conforme parce qu'il utilise un outil.

Il est conforme parce qu'il respecte les principes de l'écosystème.

---

# Les conventions communes

Tous les projets suivent un ensemble de conventions.

Par exemple :

- une responsabilité clairement identifiée ;
- une documentation minimale ;
- une API publique lorsqu'un service communique ;
- une découverte réseau basée sur morfBeacon lorsque cela est pertinent ;
- des journaux exploitables ;
- un numéro de version explicite ;
- une licence libre compatible avec l'écosystème.

Ces conventions évoluent peu.

Elles constituent la stabilité de morfSystem.

---

# Les contrats

Les contrats sont au cœur de l'architecture.

Ils définissent la manière dont les composants collaborent.

Ils ne décrivent jamais leur fonctionnement interne.

Un contrat peut concerner :

- une API HTTP ;
- un format JSON ;
- un protocole réseau ;
- un fichier d'échange ;
- une convention de découverte.

Un contrat est une promesse faite aux autres composants.

Cette promesse doit rester stable.

---

# Les versions

Chaque projet possède son propre cycle de publication.

Les versions sont indépendantes.

Il n'existe pas de version globale de morfSystem.

Cette indépendance permet à chaque composant d'évoluer à son rythme.

Elle évite également les mises à jour forcées de l'ensemble de l'écosystème.

---

# Les copies vendorées

Certains protocoles sont partagés entre plusieurs plateformes incompatibles.

Dans ce cas, une copie vendorée peut être utilisée.

Cette copie est considérée comme une projection d'un dépôt de référence.

Elle n'est jamais modifiée directement.

Toute évolution est réalisée dans le projet d'origine avant synchronisation.

Cette règle garantit qu'il n'existe qu'une seule source de vérité.

---

# morfTools

morfTools accompagne le cycle de vie des projets.

Il facilite notamment :

- l'installation ;
- la compilation ;
- la mise à jour ;
- le diagnostic ;
- la synchronisation des conventions ;
- les vérifications de cohérence.

Il n'est cependant jamais indispensable au fonctionnement des composants.

Son rôle est d'assister les développeurs et les utilisateurs.

Jamais de piloter les services.

---

# doctor

doctor constitue le principal outil de diagnostic de l'écosystème.

Son objectif n'est pas uniquement de détecter les erreurs.

Il cherche également à identifier les écarts entre un système réel et les conventions de morfSystem.

Les informations produites par doctor permettent de maintenir un environnement cohérent, quel que soit le nombre de composants installés.

---

# Les évolutions

Une évolution importante doit respecter trois principes.

Elle doit :

- conserver la simplicité de l'écosystème ;
- préserver les contrats existants autant que possible ;
- apporter davantage de valeur que de complexité.

Une évolution qui ne respecte pas ces principes doit être reconsidérée.

---

# Les nouveaux projets

Un nouveau projet n'est pas créé parce qu'une idée apparaît.

Il est créé lorsqu'une nouvelle responsabilité est clairement identifiée.

Avant la création d'un dépôt, il convient donc de répondre à plusieurs questions :

- Quelle est sa responsabilité ?
- Existe-t-il déjà un projet qui répond à ce besoin ?
- Cette responsabilité restera-t-elle pertinente dans le temps ?
- Peut-il fonctionner de manière autonome ?

Si ces questions ne trouvent pas de réponse satisfaisante, il est probable qu'un nouveau dépôt ne soit pas nécessaire.

---

# La cohérence avant la croissance

Le succès de morfSystem ne se mesure pas au nombre de dépôts.

Ni au nombre de fonctionnalités.

Il se mesure à la capacité de l'écosystème à rester simple malgré son évolution.

Préserver cette cohérence constitue l'objectif principal de sa gouvernance.