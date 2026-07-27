# Glossaire

Les mots employés dans la documentation de morfSystem ont un sens précis.

Ce glossaire les réunit, pour lever toute ambiguïté.

Chaque définition renvoie, si besoin, vers le document qui la développe.

---

## Écosystème

L'ensemble des projets morfSystem.

Ce n'est ni une application, ni une suite : c'est un ensemble de composants
indépendants qui partagent une philosophie et des contrats communs.

> Voir [Architecture](ARCHITECTURE.md).

---

## Composant

Un projet morfSystem, quel que soit son type.

Chaque composant possède son propre dépôt, sa documentation et son cycle de vie.

C'est le terme générique qui recouvre les services, les applications, les
composants embarqués et les outils.

---

## Service

Un composant qui porte une responsabilité précise et l'expose, en général via une
API HTTP, en annonçant sa présence sur le réseau.

Un service reste le **propriétaire** des données qu'il produit.

---

## Application

Un composant qui répond à un besoin métier de l'utilisateur.

Elle peut s'appuyer sur des services, mais reste utilisable seule.

Une application ne possède pas de données métier : elle les délègue aux services.

---

## Composant embarqué

Un composant qui tourne sur une plateforme contrainte, comme un ESP32.

Il produit ou collecte une information en respectant les mêmes contrats que les
autres composants.

---

## Outil

Un composant qui accompagne le cycle de vie des projets (installation,
déploiement, diagnostic).

Il ne participe pas au fonctionnement quotidien des services. `morfTools` en est
l'exemple.

---

## Responsabilité

La raison d'être d'un composant, résumable en une seule phrase.

Une responsabilité est stable dans le temps, contrairement aux fonctionnalités,
qui évoluent. L'architecture de morfSystem est organisée autour des
responsabilités, jamais autour des technologies.

> Voir [Principes d'architecture](ARCHITECTURE-PRINCIPLES.md).

---

## Contrat

Une promesse publique qu'un composant fait aux autres.

Un contrat décrit *ce qu'il garantit*, jamais *comment il fonctionne à
l'intérieur*. Il peut prendre la forme d'une API HTTP, d'un format JSON, d'un
protocole réseau ou d'un fichier d'échange.

Un contrat évolue lentement et de façon compatible ; c'est ce qui protège
l'écosystème.

> Voir [Les contrats](CONTRACTS.md).

---

## Capacité

Ce qu'un composant **sait faire**, exprimé par un identifiant stable.

On découvre un service par sa capacité, jamais par son nom : le nom peut être
modifié par l'utilisateur, la capacité non.

---

## Autonomie

La propriété d'un composant à être utile **seul**, sans aucun autre.

L'autonomie précède toujours la coopération. La coopération enrichit un composant ;
elle ne conditionne jamais son fonctionnement.

---

## Découverte

Le mécanisme par lequel un composant repère les autres sur le réseau local, sans
aucune adresse configurée à l'avance.

Dans morfSystem, la découverte repose sur `morfBeacon`.

---

## morfBeacon

Le protocole commun de découverte de l'écosystème.

Il ne transporte jamais les données métier. Il permet seulement à un composant
d'annoncer : « je suis présent, voici qui je suis, voici ce que je sais faire ».

---

## Heartbeat (présence)

Un petit message diffusé périodiquement en UDP par un composant, pour annoncer sa
**présence**.

Il est volontairement minimal, car il est émis souvent. Le détail, lui, se lit à
la demande via `/status`.

---

## /status (détail)

Un endpoint HTTP par lequel un composant publie son **détail** : état, version,
capacités, API.

Il est interrogé à la demande, donc il peut être riche. Cette séparation entre
présence (fréquente, minimale) et détail (à la demande, riche) est ce qui permet
au protocole de rester stable.

---

## Source de vérité (propriétaire de la donnée)

Le composant unique qui **possède** une information.

Les autres peuvent la consulter, la copier, l'enrichir, l'analyser, mais n'en
deviennent jamais propriétaires. Une donnée a toujours un seul propriétaire.

---

## Copie vendorée

Une duplication **volontaire** d'un protocole ou d'une bibliothèque à l'intérieur
d'un projet qui la consomme.

Elle garantit l'indépendance des dépôts et la reproductibilité des compilations,
notamment entre plateformes incompatibles. Elle n'est jamais modifiée
directement : la correction se fait dans le dépôt d'origine, puis on
resynchronise.

> Voir [Les contrats](CONTRACTS.md) et [Gouvernance](GOVERNANCE.md).

---

## morfTools

L'outil de gouvernance technique de l'écosystème.

Aucun composant n'en dépend pour fonctionner. Dès que plusieurs projets sont
installés, il garantit leur cohérence : ports réservés, dépôts officiels, copies
vendorées, déploiement, mises à jour, diagnostics.

---

## doctor

L'outil de diagnostic de `morfTools`.

Il ne se contente pas de détecter des erreurs : il repère les **écarts** entre un
système réel et les conventions de l'écosystème.

---

## Registre d'adressage (ports)

La liste unique où chaque service **réserve son port** avant tout déploiement.

Elle évite que deux services entrent en collision sur une même machine. Ce registre
est tenu par `morfTools`.
