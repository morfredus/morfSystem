# Règles de développement

Les projets composant morfSystem sont développés indépendamment.

Ils utilisent des langages différents, des plateformes différentes et répondent à des besoins différents.

Pourtant, ils doivent rester capables de collaborer naturellement.

Cette cohérence ne repose pas sur une bibliothèque commune.

Elle repose sur un ensemble de règles d'architecture volontairement simples et stables.

Ce document décrit ces règles.

---

# Les responsabilités

## Une responsabilité par projet

Chaque dépôt correspond à une responsabilité clairement identifiée.

Cette responsabilité doit pouvoir être résumée en une phrase.

Si plusieurs phrases deviennent nécessaires, le projet est probablement devenu plusieurs projets.

Exemples :

- morfMonitor supervise un système.
- morfBeacon annonce un service.
- morfNotify diffuse des notifications.
- morfAnalytics produit des analyses.
- morfSync synchronise des données.

Aucun de ces projets ne doit chercher à remplir les responsabilités des autres.

---

## Les responsabilités ne se recouvrent pas

Deux projets ne doivent jamais résoudre le même problème.

Lorsqu'un besoin apparaît dans plusieurs composants, deux solutions existent :

- déplacer cette responsabilité vers un composant dédié ;
- partager un protocole plutôt qu'une implémentation.

La duplication de logique est un défaut d'architecture.

---

# Les dépendances

## Les dépendances sont toujours optionnelles

Un projet peut découvrir un autre composant.

Il peut enrichir son fonctionnement grâce à lui.

Il ne doit jamais en dépendre pour assurer sa fonction principale.

L'absence d'un composant ne constitue jamais une erreur.

Elle signifie simplement que certaines fonctionnalités complémentaires ne sont pas disponibles.

---

## Les dépendances sont publiques

Aucune dépendance implicite ne doit exister.

Lorsqu'un projet utilise un autre service, cette relation doit être visible, documentée et reposer sur une API publique.

Aucun accès direct aux structures internes d'un autre projet n'est autorisé.

---

# Les communications

## Les échanges utilisent des contrats

Les composants communiquent grâce à des contrats stables.

Ces contrats sont indépendants des implémentations.

Changer une implémentation ne doit pas modifier les contrats.

---

## Les API sont publiques

Lorsqu'un service expose une API HTTP, celle-ci fait partie de son contrat.

Elle doit être :

- documentée ;
- stable ;
- versionnée lorsque cela devient nécessaire.

Une API est considérée comme une interface publique.

Elle ne doit pas évoluer sans justification.

---

## Les formats sont simples

Les échanges privilégient des formats ouverts.

Par exemple :

- HTTP
- JSON
- UDP
- fichiers texte
- CSV lorsque pertinent

Les formats propriétaires sont évités.

---

# La découverte

## Aucun composant ne connaît les autres

Les services ne possèdent aucune liste codée en dur.

Ils découvrent dynamiquement leurs voisins.

Cette règle permet d'ajouter ou supprimer un service sans modifier le reste de l'écosystème.

---

## La découverte repose sur morfBeacon

morfBeacon constitue le protocole officiel de découverte.

Il ne transporte pas les données métier.

Il annonce simplement :

- l'identité du service ;
- son état ;
- sa description ;
- les informations nécessaires à sa découverte.

---

# La configuration

Chaque projet reste propriétaire de sa configuration.

Les conventions sont néanmoins communes.

Les fichiers de configuration doivent être :

- lisibles ;
- documentés ;
- facilement sauvegardables ;
- indépendants du code.

Les chemins suivent les conventions du système d'exploitation.

---

# Les erreurs

Une erreur d'un composant ne doit jamais provoquer l'arrêt d'un autre.

Les erreurs doivent être :

- détectables ;
- compréhensibles ;
- journalisées.

Les comportements silencieux sont évités.

---

# Les journaux

Les journaux constituent un outil de diagnostic.

Ils doivent permettre de comprendre ce qu'un service fait.

Ils ne doivent pas devenir un flux de débogage permanent.

Les messages doivent privilégier :

- les changements d'état ;
- les erreurs ;
- les décisions importantes.

---

# La supervision

Chaque service doit pouvoir être observé.

La supervision ne modifie jamais le comportement d'un service.

Observer n'est pas contrôler.

Cette distinction est fondamentale.

---

# Les évolutions

Une nouvelle fonctionnalité doit répondre à une question simple :

Apporte-t-elle davantage de valeur qu'elle n'ajoute de complexité ?

Si la réponse est incertaine, la fonctionnalité attend.

---

# Les technologies

Les technologies utilisées importent moins que leur stabilité.

Le projet privilégie :

- les standards ouverts ;
- les dépendances limitées ;
- les implémentations lisibles ;
- les solutions pérennes.

Une technologie n'est jamais choisie parce qu'elle est récente.

Elle est choisie parce qu'elle constitue la meilleure réponse au problème posé.

---

# Les plateformes

Les composants peuvent cibler :

- Linux
- Windows
- ESP32
- Raspberry Pi

Ils partagent une philosophie.

Pas une implémentation.

---

# Les copies vendorées

Lorsqu'un protocole est partagé entre plusieurs plateformes incompatibles, une copie vendorée est privilégiée.

Cette copie constitue une duplication volontaire.

Elle garantit :

- l'indépendance des dépôts ;
- la stabilité des compilations ;
- la lisibilité des implémentations.

Les corrections sont toujours réalisées dans le dépôt d'origine avant synchronisation.

---

# La gouvernance

La gouvernance appartient à l'écosystème.

Elle n'appartient à aucun composant.

morfTools facilite cette gouvernance.

Il ne la définit pas.

Les conventions restent valables même en son absence.

---

# Le principe fondamental

La meilleure fonctionnalité est celle qui ne complique pas l'écosystème.

Chaque nouveau projet doit chercher à rendre morfSystem plus simple à comprendre.

Jamais l'inverse.