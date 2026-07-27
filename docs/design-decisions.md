# Les décisions d'architecture

Toutes les architectures résultent d'une succession de décisions.

Certaines sont techniques.

D'autres sont philosophiques.

Dans morfSystem, les décisions les plus importantes ne concernent pas les langages utilisés, les bibliothèques ou les protocoles.

Elles concernent la manière de concevoir les logiciels.

Ce document explique les principales décisions d'architecture de l'écosystème.

---

# Pourquoi plusieurs dépôts ?

Il serait techniquement plus simple de regrouper tous les composants dans un unique dépôt.

Ce choix a volontairement été rejeté.

Chaque dépôt représente une responsabilité indépendante.

Cette séparation facilite :

- l'évolution indépendante des composants ;
- la lisibilité du code ;
- la maintenance ;
- la réutilisation de certains services ;
- la publication de versions indépendantes.

Un dépôt n'est jamais créé pour "organiser le code".

Il est créé lorsqu'une nouvelle responsabilité apparaît.

---

# Pourquoi un service = une responsabilité ?

Une responsabilité évolue beaucoup moins vite qu'une fonctionnalité.

Les fonctionnalités apparaissent.

Évoluent.

Disparaissent.

Les responsabilités, elles, restent remarquablement stables.

Concevoir l'architecture autour des responsabilités produit naturellement des composants plus simples.

Plus prévisibles.

Et beaucoup plus faciles à maintenir.

---

# Pourquoi des services autonomes ?

Une dépendance obligatoire crée un point de rupture.

Lorsqu'un service devient indispensable à un autre, la résilience de l'ensemble diminue.

morfSystem privilégie donc un autre modèle.

Chaque projet possède une valeur intrinsèque.

La coopération améliore les fonctionnalités.

Elle ne conditionne jamais leur existence.

---

# Pourquoi HTTP ?

HTTP n'est probablement pas le protocole le plus performant.

Ce n'est pas son objectif.

HTTP possède trois qualités importantes :

- universel ;
- documenté ;
- compréhensible.

Une API HTTP peut être testée avec un navigateur.

Avec curl.

Avec un simple script.

Cette simplicité réduit énormément la complexité de maintenance.

---

# Pourquoi JSON ?

JSON est lisible.

Portable.

Documenté.

Il existe des implémentations sur toutes les plateformes utilisées par morfSystem.

Le choix est donc avant tout pragmatique.

---

# Pourquoi morfBeacon ?

Les services devaient pouvoir se découvrir automatiquement.

Plusieurs solutions existaient.

mDNS.

DNS-SD.

UPnP.

Protocoles propriétaires.

Aucune ne répondait exactement aux besoins de l'écosystème.

morfBeacon est donc devenu un protocole extrêmement simple.

Il ne cherche pas à transporter les données métier.

Il annonce simplement qu'un service existe.

Puis laisse HTTP prendre le relais.

Cette séparation entre découverte et communication simplifie fortement l'architecture.

---

# Pourquoi un protocole minimal ?

Un protocole est beaucoup plus difficile à faire évoluer qu'une implémentation.

Plus il transporte d'informations, plus son évolution devient délicate.

morfBeacon transporte donc uniquement ce qui est nécessaire à la découverte.

Les informations détaillées sont publiées via HTTP.

---

# Pourquoi les copies vendorées ?

Le partage de code entre plateformes très différentes devient rapidement compliqué.

ESP32.

Qt.

Linux.

Windows.

Les mêmes bibliothèques ne peuvent pas toujours être utilisées.

La duplication volontaire d'un protocole stable est donc parfois préférable à une dépendance complexe.

Les corrections sont réalisées dans le dépôt d'origine.

Les copies sont ensuite resynchronisées.

Cette duplication est assumée.

Elle est documentée.

Elle est contrôlée.

---

# Pourquoi morfTools n'est-il pas obligatoire ?

Parce qu'un outil d'administration ne doit jamais devenir un point de défaillance.

morfTools facilite :

- les installations ;
- les mises à jour ;
- les diagnostics ;
- la gouvernance.

Mais un service installé doit continuer à fonctionner même si morfTools n'est jamais utilisé.

Cette indépendance est volontaire.

---

# Pourquoi le réseau local est-il la référence ?

morfSystem est conçu pour fonctionner chez son utilisateur.

Le fonctionnement nominal ne dépend d'aucune infrastructure distante.

Le réseau local représente donc la situation normale.

L'accès distant est considéré comme une extension.

Jamais comme une obligation.

---

# Pourquoi autant de documentation ?

La documentation ne décrit pas seulement le logiciel.

Elle décrit les décisions.

Le code explique comment fonctionne un projet.

La documentation explique pourquoi il fonctionne ainsi.

Ces deux informations sont complémentaires.

---

# Pourquoi tant d'importance accordée aux responsabilités ?

Parce que les responsabilités survivent aux technologies.

Les bibliothèques changent.

Les frameworks disparaissent.

Les langages évoluent.

Une responsabilité bien définie reste généralement pertinente pendant des années.

L'architecture de morfSystem est donc organisée autour des responsabilités.

Jamais autour des technologies.

---

# Une architecture assumée

Toutes les décisions présentées dans ce document pourraient être remises en question.

Aucune n'est considérée comme universelle.

En revanche, elles sont cohérentes entre elles.

Cette cohérence constitue probablement la caractéristique la plus importante de morfSystem.

Une décision isolée peut sembler discutable.

L'ensemble forme une architecture volontairement simple, stable et compréhensible.

C'est cette cohérence qui guide l'évolution de l'écosystème.