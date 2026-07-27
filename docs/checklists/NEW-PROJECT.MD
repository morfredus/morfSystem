# Checklist de création d'un nouveau projet

Cette checklist doit être parcourue avant la création de tout nouveau dépôt morfSystem.

Son objectif n'est pas de ralentir le développement.

Son objectif est d'éviter la création de composants inutiles.

Si plusieurs réponses sont négatives, il est probable qu'un nouveau projet ne soit pas nécessaire.

---

# La responsabilité

☐ Puis-je résumer la responsabilité du projet en une seule phrase ?

☐ Cette responsabilité est-elle différente de celles des projets existants ?

☐ Cette responsabilité restera-t-elle probablement pertinente dans plusieurs années ?

☐ Un utilisateur peut-il comprendre immédiatement pourquoi ce projet existe ?

---

# L'autonomie

☐ Le projet est-il utile lorsqu'il est installé seul ?

☐ Peut-il fonctionner sans aucun autre composant morfSystem ?

☐ Les autres services améliorent-ils simplement son fonctionnement ?

---

# Les données

☐ Le projet produit-il des données ?

Si oui :

☐ Est-il clairement identifié comme leur source de vérité ?

Sinon :

☐ Les données proviennent-elles d'un autre composant ?

☐ Le projet respecte-t-il cette propriété ?

---

# Les communications

☐ Une API HTTP est-elle réellement nécessaire ?

☐ Les formats utilisés sont-ils simples ?

☐ Les contrats sont-ils clairement identifiés ?

☐ Les futurs changements resteront-ils compatibles ?

---

# Les dépendances

☐ Une dépendance peut-elle être supprimée ?

☐ Une bibliothèque est-elle utilisée uniquement parce qu'elle est populaire ?

☐ Existe-t-il une solution plus simple ?

☐ Le projet fonctionnera-t-il toujours si un autre service disparaît ?

---

# L'écosystème

☐ Le projet enrichit-il réellement morfSystem ?

☐ Introduit-il une nouvelle responsabilité ?

☐ Évite-t-il de dupliquer un composant existant ?

☐ Les interactions avec les autres projets restent-elles simples ?

---

# La maintenance

☐ Ce projet sera-t-il encore compréhensible dans cinq ans ?

☐ Sa documentation peut-elle être rédigée facilement ?

☐ Son périmètre est-il clairement défini ?

☐ Son nom reflète-t-il correctement sa responsabilité ?

---

# Avant la création du dépôt

Si une seule de ces questions reste sans réponse claire, il est préférable de poursuivre la réflexion avant de créer un nouveau dépôt.

Créer un dépôt est une décision d'architecture.

Pas une décision d'organisation.

Chaque nouveau composant augmente la richesse de l'écosystème.

Il augmente également sa complexité.

Cette complexité doit toujours être justifiée.