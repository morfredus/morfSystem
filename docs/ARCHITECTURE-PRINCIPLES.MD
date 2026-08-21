# Les principes d'architecture

Les principes décrits dans ce document constituent le socle de morfSystem.

Ils ne décrivent pas comment écrire du code.

Ils décrivent comment concevoir un composant.

Les technologies évolueront.

Les bibliothèques changeront.

Les langages disparaîtront.

Ces principes, eux, ont vocation à rester stables.

---

# Premier principe
## Une responsabilité unique

Chaque projet existe pour une seule raison.

Cette raison doit être clairement identifiable.

Lorsqu'un composant commence à poursuivre plusieurs objectifs indépendants, il devient difficile à comprendre, à maintenir et à faire évoluer.

La solution consiste généralement à créer un nouveau projet.

Jamais à complexifier l'ancien.

---

# Deuxième principe
## L'autonomie avant la coopération

Un composant doit être utile même lorsqu'il est seul.

La coopération avec les autres services constitue une amélioration.

Jamais une condition de fonctionnement.

L'autonomie représente la première garantie de robustesse.

---

# Troisième principe
## Les contrats avant les implémentations

Les composants collaborent grâce à leurs interfaces publiques.

Jamais grâce à leur fonctionnement interne.

Une implémentation peut être entièrement réécrite.

Le contrat, lui, doit rester stable.

C'est cette stabilité qui protège l'écosystème.

---

# Quatrième principe
## La simplicité est une fonctionnalité

Chaque ligne de code possède un coût.

Chaque dépendance possède un coût.

Chaque fonctionnalité possède un coût.

La simplicité ne consiste pas à écrire moins de code.

Elle consiste à résoudre le problème avec le minimum de complexité nécessaire.

---

# Cinquième principe
## Le réseau local est le fonctionnement nominal

Les services sont conçus pour fonctionner sur le réseau de leur utilisateur.

Ils ne nécessitent aucune infrastructure distante.

Les accès externes sont possibles.

Ils constituent une extension.

Jamais une obligation.

---

# Sixième principe
## Les standards avant les solutions propriétaires

Lorsqu'un standard ouvert répond au besoin, il est privilégié.

HTTP.

JSON.

CSV.

TCP.

UDP.

Le recours à une technologie spécifique doit toujours être justifié.

Jamais subi.

---

# Septième principe
## Observer plutôt que contrôler

L'observation et le pilotage constituent deux responsabilités différentes.

Les outils de supervision observent.

Ils collectent.

Ils présentent.

Ils n'interviennent pas sur le comportement des autres services.

Cette séparation améliore la sécurité, la lisibilité et la robustesse.

---

# Huitième principe
## La donnée appartient toujours à quelqu'un

Une information possède toujours un propriétaire.

Un seul.

Les autres composants peuvent :

- la consulter ;
- la copier ;
- l'enrichir ;
- l'analyser.

Ils ne deviennent jamais propriétaires de cette donnée.

Cette règle évite les incohérences.

PhotoHub × morfPhoto et SiteWatch × morfAnalytics l'ont vérifiée sur le parc :
voir [Validation réelle](VALIDATION-REELLE-MORFSYSTEM.md).

---

# Neuvième principe
## La duplication volontaire est préférable à la dépendance forcée

Certaines duplications sont bénéfiques.

Les copies vendorées en sont un exemple.

Elles permettent de préserver :

- l'autonomie ;
- la reproductibilité ;
- la stabilité.

La duplication n'est pas un défaut lorsqu'elle répond à un objectif clairement identifié.

---

# Dixième principe
## Les responsabilités survivent aux technologies

Une technologie peut devenir obsolète.

Une responsabilité reste.

Les décisions d'architecture doivent donc être prises en fonction des responsabilités.

Jamais des outils disponibles à un instant donné.

---

# Onzième principe
## La cohérence prime sur l'innovation

Une nouveauté n'est intéressante que si elle améliore réellement l'écosystème.

L'objectif n'est pas d'utiliser les dernières technologies.

L'objectif est de construire un ensemble cohérent.

Une solution éprouvée est souvent préférable à une solution plus récente mais plus complexe.

---

# Douzième principe
## L'écosystème est le produit

Chaque dépôt possède sa propre valeur.

Mais aucun dépôt n'est l'objectif final.

Le véritable produit est l'ensemble.

Les composants sont conçus pour vivre ensemble sans perdre leur indépendance.

Cette cohérence constitue l'identité de morfSystem.

Elle guide toutes les décisions d'architecture.