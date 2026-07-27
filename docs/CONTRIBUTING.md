# Contribuer à morfSystem

morfSystem est un écosystème de projets **libres** et **indépendants**.

Contribuer ne veut pas dire modifier un dépôt central : il n'y en a pas.

Cela veut dire enrichir l'écosystème tout en respectant ce qui fait sa cohérence.

Ce document explique comment participer, et surtout ce qu'il ne faut jamais faire.

---

# Trois façons de participer

## 1. Améliorer un composant existant

Chaque projet possède son propre dépôt, sa propre documentation et son propre
cycle de publication.

Une correction, une amélioration ou une clarification se propose **dans le dépôt
concerné**, jamais ici.

Ce dépôt-ci ne contient que la documentation d'architecture commune.

## 2. Créer un nouveau composant

Un nouveau composant n'apparaît pas parce qu'une idée est intéressante.

Il apparaît lorsqu'une **responsabilité** nouvelle est clairement identifiée.

Avant de créer un dépôt, il faut donc parcourir la
[checklist de création](checklists/NEW-PROJECT.md) et suivre le
[cycle de vie d'un projet](PROJECT-LIFECYCLE.md).

## 3. Écrire sa propre implémentation compatible

Les contrats sont **publics**.

Toute implémentation qui les respecte peut rejoindre l'écosystème, quel que soit
son langage ou sa plateforme.

Voir [les contrats](CONTRACTS.md).

---

# Avant d'écrire du code

Poser trois questions, dans cet ordre :

1. **Quel problème ?** Une contribution part d'un besoin, jamais d'une technologie.

2. **Quelle responsabilité ?** Elle doit tenir en une phrase, et ne pas recouvrir
   celle d'un composant existant.

3. **Est-ce autonome ?** Le composant doit rendre service seul ; la coopération
   n'est qu'un bonus.

Le [cadre de décision](DECISION-FRAMEWORK.md) détaille ce raisonnement.

---

# Les conventions à respecter

Une contribution s'aligne sur les conventions communes :

- une **responsabilité unique**, résumable en une phrase ;

- une **API publique** documentée dès qu'un service communique ;

- une **découverte** via morfBeacon lorsqu'elle est pertinente ;

- des **formats ouverts** (HTTP, JSON, texte) ;

- un **rangement des données** conforme à la convention
  ([config / data / cache / logs / tmp](FILESYSTEM.md)) ;

- un **numéro de version** explicite et une **licence libre** compatible ;

- des **journaux** exploitables, sans flux de débogage permanent.

Le détail vit dans les [règles de développement](DEVELOPMENT-RULES.md) et la
[gouvernance](GOVERNANCE.md).

---

# Les interdits

Ces règles ne sont pas des préférences.

Les enfreindre, c'est rompre ce qui rend l'écosystème cohérent.

- **Ne jamais créer de dépendance obligatoire.** L'absence d'un composant retire
  une possibilité, jamais le fonctionnement de base d'un autre.

- **Ne jamais accéder à l'intérieur d'un autre projet.** On passe par son API
  publique, jamais par ses fichiers ou structures internes.

- **Ne jamais dupliquer une responsabilité** déjà portée par un composant
  existant. On partage un contrat, pas une implémentation recopiée.

- **Ne jamais introduire de composant central** qui piloterait les autres.
  L'écosystème n'a pas de maître.

- **Ne jamais rendre morfTools indispensable.** Un service installé doit
  fonctionner même si morfTools n'est jamais utilisé.

- **Ne jamais s'approprier la donnée d'autrui.** Une information a un seul
  propriétaire ; les autres la consultent, la copient, l'enrichissent.

- **Ne jamais découvrir un service par son nom.** On cherche une **capacité** ; le
  nom peut changer, la capacité non.

- **Ne jamais casser un contrat en silence.** Un contrat évolue de façon
  compatible, ou change de version. Jamais sans le dire.

- **Ne jamais modifier directement une copie vendorée.** La correction se fait
  dans le dépôt d'origine, puis on resynchronise.

- **Ne jamais confondre observer et contrôler.** Un outil de supervision observe ;
  il ne donne pas d'ordre.

- **Ne jamais échouer en silence.** Une erreur se détecte, se comprend et se
  journalise.

- **Ne jamais ajouter une technologie « parce qu'elle est récente ».** On la
  choisit parce qu'elle est la meilleure réponse au problème.

Ces interdits prolongent, côté contribution, ce que décrit
[Ce que morfSystem n'est pas](NON-GOALS.md).

---

# Proposer un changement

Chaque projet étant indépendant, une contribution se discute **dans le dépôt du
projet concerné** : une issue pour signaler ou proposer, une pull request pour
soumettre un changement.

Une bonne proposition explique d'abord **le problème**, ensuite la solution.

Elle montre en quoi elle **simplifie** l'écosystème, ou au moins ne le complique
pas.

---

# L'esprit

La meilleure contribution n'est pas celle qui ajoute le plus.

C'est celle qui laisse l'écosystème **plus simple à comprendre** qu'avant.

Avant de proposer un changement, une dernière question :

> Dans cinq ans, sera-t-il encore évident de comprendre pourquoi ce changement a
> été fait ?

Si la réponse est oui, la contribution a probablement trouvé sa place.
