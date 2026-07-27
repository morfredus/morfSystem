# Penser en morfSystem

Apprendre morfSystem ne consiste pas à apprendre une API.

Ni un framework.

Ni une bibliothèque.

Apprendre morfSystem consiste à adopter une manière de réfléchir à la conception d'un logiciel.

Cette manière de penser est souvent plus importante que les technologies utilisées.

---

# Commencer par le problème

Dans de nombreux projets, le développement commence par une idée.

Dans morfSystem, il commence par une question.

Quel problème souhaite-t-on réellement résoudre ?

Cette question paraît simple.

Elle évite pourtant de nombreuses erreurs.

Un projet ne doit jamais exister parce qu'une technologie est intéressante.

Il doit exister parce qu'un besoin est clairement identifié.

---

# Chercher la responsabilité

Une fois le problème identifié, il faut oublier les fonctionnalités.

La question devient :

Qui est responsable de ce problème ?

Cette recherche est fondamentale.

Une responsabilité est stable.

Les fonctionnalités qui en découlent évolueront naturellement.

---

# Imaginer un composant isolé

Avant de penser aux interactions avec les autres projets, il faut imaginer le composant totalement seul.

Serait-il déjà utile ?

Pourrait-il rendre service sans aucune autre dépendance ?

Si la réponse est non, le projet est probablement mal défini.

L'autonomie précède toujours la collaboration.

---

# Ajouter la coopération

Une fois autonome, le composant peut commencer à dialoguer avec les autres.

Mais cette coopération reste facultative.

Elle enrichit.

Elle ne conditionne jamais le fonctionnement.

Chaque nouveau lien entre deux composants doit être justifié.

Un lien inutile est une complexité supplémentaire.

---

# Se demander qui possède la donnée

Avant d'échanger une information, une question doit être posée.

Qui en est propriétaire ?

Cette réponse doit toujours être unique.

Les autres composants peuvent :

- consulter ;
- copier ;
- analyser ;
- transformer.

Ils ne deviennent jamais propriétaires de cette donnée.

Cette règle simplifie énormément les architectures distribuées.

---

# Préférer les contrats

Les composants ne doivent jamais connaître leurs détails internes.

Ils ne doivent connaître qu'une promesse.

Cette promesse prend généralement la forme :

- d'une API ;
- d'un format ;
- d'un protocole.

Le reste ne les concerne pas.

---

# Accepter les limites

Tous les projets ne doivent pas tout faire.

Une responsabilité implique également des renoncements.

Dire :

> « Ce n'est pas le rôle de ce projet. »

est souvent une excellente décision d'architecture.

---

# La simplicité demande du travail

La simplicité ne résulte pas de l'absence de réflexion.

Elle en est souvent la conséquence.

Supprimer une dépendance.

Fusionner deux concepts.

Clarifier une responsabilité.

Retirer une fonctionnalité inutile.

Toutes ces décisions demandent davantage de réflexion qu'ajouter une nouvelle option.

---

# Penser à demain

Une architecture ne se juge pas uniquement aujourd'hui.

Elle doit également rester compréhensible dans plusieurs années.

Avant chaque décision importante, une question peut être posée.

Dans cinq ans, sera-t-il encore évident de comprendre pourquoi cette décision a été prise ?

Si la réponse est non, il faut probablement revoir la conception.

---

# Le meilleur composant

Le meilleur composant n'est pas celui qui possède le plus de fonctionnalités.

C'est celui dont la responsabilité est immédiatement comprise.

Lorsqu'un développeur découvre un projet morfSystem, il devrait pouvoir répondre en quelques secondes à une seule question :

Pourquoi ce projet existe-t-il ?

Si cette réponse est claire, alors le reste devient généralement simple.

---

# Une philosophie avant une architecture

Les composants de morfSystem ne sont pas liés par leur langage.

Ni par leur système d'exploitation.

Ni par leurs bibliothèques.

Ils sont liés par une manière commune de concevoir les logiciels.

Cette philosophie est ce qui permet à l'écosystème de grandir sans perdre son identité.

Chaque nouveau projet apporte une nouvelle capacité.

Jamais une nouvelle manière de penser.

C'est cette continuité qui fait de morfSystem un écosystème plutôt qu'une simple collection de logiciels.