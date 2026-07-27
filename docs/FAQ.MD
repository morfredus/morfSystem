# Questions fréquentes

Cette FAQ répond aux questions qui reviennent le plus souvent à propos de l'architecture de morfSystem.

---

# Pourquoi plusieurs dépôts ?

Parce que chaque projet possède une responsabilité propre.

Un dépôt représente un composant autonome.

Cela facilite :

- la maintenance ;
- les versions ;
- la documentation ;
- les tests ;
- la réutilisation.

Les projets évoluent indépendamment.

---

# Pourquoi ne pas créer un gros projet unique ?

Un projet unique devient progressivement difficile à maintenir.

Les responsabilités se mélangent.

Les dépendances augmentent.

Les cycles de publication deviennent liés.

En séparant les responsabilités, chaque projet reste simple et autonome.

---

# Pourquoi HTTP ?

HTTP est :

- universel ;
- documenté ;
- simple à tester ;
- indépendant du langage ;
- disponible sur toutes les plateformes utilisées par morfSystem.

Il permet également de rendre chaque service directement observable.

---

# Pourquoi JSON ?

JSON est lisible.

Simple.

Portable.

Disponible sur toutes les plateformes.

Il constitue un excellent format d'échange entre composants hétérogènes.

---

# Pourquoi ne pas utiliser MQTT ?

MQTT est un excellent protocole.

Il répond cependant à des besoins différents.

morfSystem privilégie des échanges directs entre composants autonomes.

L'architecture ne nécessite pas de courtier central.

Lorsque MQTT apporte une réelle valeur, rien n'interdit son utilisation dans un projet particulier.

Il ne constitue simplement pas la base de l'écosystème.

---

# Pourquoi morfBeacon ?

Les composants doivent pouvoir se découvrir automatiquement.

morfBeacon fournit cette découverte.

Il évite la configuration manuelle des adresses.

Il facilite également la supervision de l'écosystème.

---

# Pourquoi chaque projet possède-t-il sa propre documentation ?

Parce que chaque projet est autonome.

La documentation de morfSystem décrit l'architecture.

La documentation des projets décrit leur fonctionnement.

Cette séparation évite qu'un dépôt devienne la documentation de tous les autres.

---

# Pourquoi autant parler de responsabilités ?

Parce que la responsabilité constitue l'unité fondamentale de l'architecture.

Les fonctionnalités évoluent.

Les responsabilités restent.

Un projet bien défini est plus facile à comprendre, maintenir et faire évoluer.

---

# Pourquoi les contrats sont-ils si importants ?

Les composants ne partagent pas leur implémentation.

Ils partagent uniquement des contrats.

Ces contrats permettent à chaque projet d'évoluer indépendamment sans casser les autres.

---

# Pourquoi privilégier l'autonomie ?

Un composant utile uniquement lorsqu'il dépend de plusieurs autres devient difficile à maintenir.

Chaque projet doit conserver une valeur propre.

Les collaborations viennent ensuite.

---

# Pourquoi ne pas utiliser Docker partout ?

Docker est un excellent outil.

Il n'est cependant pas une condition de fonctionnement de morfSystem.

L'écosystème doit rester utilisable directement sur les plateformes qu'il cible.

Des conteneurs peuvent être proposés lorsqu'ils simplifient réellement le déploiement.

Ils ne constituent pas une obligation.

---

# Pourquoi ne pas utiliser une base de données commune ?

Chaque composant reste propriétaire de ses données.

Partager une base de données créerait un couplage fort.

Les échanges passent donc par des contrats clairement définis.

---

# Pourquoi autant documenter ?

Parce que les décisions d'architecture vivent plus longtemps que le code.

Le code explique comment fonctionne un composant.

La documentation explique pourquoi il fonctionne ainsi.

Les deux sont complémentaires.

---

# Puis-je utiliser uniquement un composant ?

Oui.

C'est même l'objectif.

Chaque projet doit être utile lorsqu'il est utilisé seul.

L'écosystème apporte ensuite des possibilités supplémentaires.

---

# Puis-je développer mon propre composant compatible ?

Oui.

Les contrats sont publics.

Les technologies utilisées sont libres.

Toute implémentation respectant les contrats peut rejoindre l'écosystème.

---

# morfSystem est-il terminé ?

Non.

L'écosystème continue d'évoluer.

Ce qui reste stable, ce sont les principes qui guident cette évolution.