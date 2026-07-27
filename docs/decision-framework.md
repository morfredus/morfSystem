# Prendre une décision d'architecture

Les décisions d'architecture influencent l'écosystème pendant plusieurs années.

Elles ne doivent donc jamais être prises uniquement parce qu'une solution paraît intéressante.

Avant toute décision importante, morfSystem applique un ensemble de questions.

Ces questions ne donnent pas la réponse.

Elles permettent simplement de prendre une décision cohérente avec les principes de l'écosystème.

---

# 1. Quel problème cherche-t-on à résoudre ?

Toute décision commence par un problème.

Jamais par une solution.

Si le problème ne peut pas être formulé clairement, la décision doit attendre.

---

# 2. S'agit-il réellement d'un problème d'architecture ?

Toutes les difficultés ne nécessitent pas une décision architecturale.

Certaines relèvent simplement d'un bogue.

D'autres d'une optimisation.

L'architecture ne doit évoluer que lorsque la responsabilité des composants est concernée.

---

# 3. Quelle responsabilité est concernée ?

Avant toute modification, il faut identifier la responsabilité concernée.

La décision ne doit jamais brouiller cette responsabilité.

Au contraire.

Elle doit la renforcer.

---

# 4. Cette décision augmente-t-elle les dépendances ?

Chaque nouvelle dépendance possède un coût.

Avant de l'accepter, il convient d'évaluer :

- sa nécessité ;
- sa pérennité ;
- sa complexité.

Une dépendance supprimée représente souvent une amélioration.

---

# 5. Cette décision augmente-t-elle la complexité ?

Ajouter une fonctionnalité est simple.

Réduire la complexité est difficile.

Toute décision doit donc être évaluée sous cet angle.

Si deux solutions répondent au même besoin, la plus simple est généralement privilégiée.

---

# 6. Cette décision respecte-t-elle les principes de morfSystem ?

Avant d'être validée, une décision doit être confrontée aux principes fondamentaux de l'écosystème.

Respecte-t-elle :

- l'autonomie ?
- les responsabilités ?
- les contrats ?
- la simplicité ?
- la stabilité ?

Si plusieurs principes sont remis en cause, la décision mérite probablement d'être repensée.

---

# 7. Existe-t-il une solution plus simple ?

Cette question doit toujours être posée.

Même lorsqu'une solution paraît satisfaisante.

La recherche de simplicité constitue une étape à part entière du processus de décision.

---

# 8. Quelles seront les conséquences dans cinq ans ?

Certaines décisions sont excellentes aujourd'hui.

Elles deviennent problématiques avec le temps.

Imaginer l'évolution future permet souvent d'identifier ces difficultés.

Une architecture durable pense toujours au long terme.

---

# 9. Cette décision améliore-t-elle l'écosystème ?

Une décision peut améliorer un projet.

Tout en compliquant l'ensemble.

Dans morfSystem, l'écosystème reste la référence.

L'amélioration locale ne doit jamais dégrader la cohérence globale.

---

# 10. Faut-il réellement prendre cette décision ?

Ne rien changer constitue parfois la meilleure décision.

L'absence de modification est une option légitime.

Une architecture stable n'évolue pas en permanence.

Elle évolue lorsqu'une évolution est réellement justifiée.

---

# Conclusion

Une bonne décision d'architecture ne se reconnaît pas uniquement à son résultat.

Elle se reconnaît au raisonnement qui a conduit à cette décision.

Documenter ce raisonnement constitue une partie essentielle de la gouvernance de morfSystem.