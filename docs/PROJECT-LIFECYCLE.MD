# Le cycle de vie d'un projet

Les projets de morfSystem ne naissent pas d'une idée.

Ils naissent d'un besoin clairement identifié.

Cette différence est importante.

Une idée peut être intéressante.

Un besoin devient une responsabilité.

Et seules les responsabilités justifient la création d'un nouveau projet.

---

# Identifier un besoin

Tout commence par une observation.

Quelque chose manque.

Quelque chose est réalisé plusieurs fois.

Ou une responsabilité n'est clairement portée par aucun composant existant.

La première question n'est jamais :

> Quel logiciel vais-je écrire ?

La première question est toujours :

> Quelle responsabilité manque aujourd'hui dans l'écosystème ?

Si aucune réponse claire n'existe, le projet n'est probablement pas encore mûr.

---

# Définir la responsabilité

Avant d'écrire la moindre ligne de code, la responsabilité doit pouvoir être résumée en une phrase.

Par exemple :

> morfMonitor observe un système.

Ou :

> morfSync synchronise des données.

Ou encore :

> morfBeacon annonce la présence d'un service.

Si cette phrase devient longue, ambiguë ou nécessite plusieurs verbes, c'est généralement le signe que plusieurs responsabilités sont mélangées.

---

# Vérifier l'existant

Avant de créer un nouveau dépôt, une vérification est réalisée.

Existe-t-il déjà un composant qui porte cette responsabilité ?

Si oui, il est préférable de le faire évoluer.

Créer un nouveau projet est toujours la dernière option.

---

# Créer un dépôt

Lorsqu'une nouvelle responsabilité est confirmée, un dépôt indépendant est créé.

Ce dépôt possède immédiatement :

- une licence ;
- une documentation ;
- une version ;
- une identité propre.

Il est considéré comme un projet autonome dès son premier jour.

---

# Développer progressivement

Le développement suit une logique incrémentale.

Une fonctionnalité est ajoutée.

Puis testée.

Puis stabilisée.

Avant de poursuivre.

Les grands chantiers sont volontairement découpés.

Cette approche facilite :

- les tests ;
- les retours arrière ;
- la compréhension de l'évolution du projet.

---

# Produire une première version utile

La première version ne cherche pas à être complète.

Elle cherche à être utile.

Un projet capable de résoudre correctement un problème simple possède davantage de valeur qu'un projet ambitieux mais inachevé.

---

# Observer l'utilisation

Une fois utilisé dans des conditions réelles, le projet révèle naturellement ses limites.

Les améliorations proviennent alors principalement de l'expérience.

Et non d'une liste théorique de fonctionnalités.

Cette étape est essentielle.

Elle évite de développer des fonctions qui ne seront jamais utilisées.

---

# Stabiliser

Lorsque les évolutions deviennent principalement des corrections, des optimisations ou des améliorations ergonomiques, le projet entre dans une phase de stabilisation.

Les changements deviennent plus prudents.

Les contrats se figent progressivement.

La documentation prend davantage d'importance.

---

# Intégrer à l'écosystème

Un projet mature commence alors à collaborer avec les autres composants.

Cette intégration reste progressive.

Chaque nouvelle interaction doit respecter les principes de morfSystem :

- autonomie ;
- simplicité ;
- absence de dépendance obligatoire.

---

# Maintenir

Une fois stabilisé, le projet continue d'évoluer.

Mais son évolution devient guidée par les besoins réels.

La maintenance comprend notamment :

- les corrections de bogues ;
- les évolutions de plateformes ;
- les améliorations de performances ;
- les évolutions des contrats lorsque cela devient nécessaire.

L'objectif n'est pas de publier souvent.

L'objectif est de publier lorsque cela apporte une valeur réelle.

---

# Retirer un projet

Tous les projets n'ont pas vocation à exister éternellement.

Si une responsabilité disparaît ou fusionne naturellement avec une autre, un projet peut être archivé.

Cette décision n'est pas considérée comme un échec.

Elle reflète simplement l'évolution de l'écosystème.

---

# L'écosystème avant les composants

Tout au long de son cycle de vie, un projet conserve une règle fondamentale.

Il n'existe pas pour lui-même.

Il existe pour enrichir l'écosystème.

Chaque évolution doit donc être évaluée non seulement selon son intérêt propre, mais aussi selon son impact sur la cohérence de l'ensemble.

Dans morfSystem, la qualité d'un projet ne se mesure pas uniquement à son code.

Elle se mesure également à sa capacité à trouver naturellement sa place parmi les autres composants.