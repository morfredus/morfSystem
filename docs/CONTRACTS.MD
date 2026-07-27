# Les contrats de morfSystem

L'architecture de morfSystem repose sur un principe simple.

Les composants ne se connaissent pas.

Ils connaissent uniquement les contrats qu'ils partagent.

Cette distinction est fondamentale.

Un composant peut être entièrement réécrit.

Le contrat, lui, doit rester stable.

C'est cette stabilité qui permet à l'écosystème d'évoluer sans rupture.

---

# Qu'est-ce qu'un contrat ?

Un contrat est une promesse.

Cette promesse décrit uniquement ce qu'un composant garantit aux autres.

Elle ne décrit jamais son implémentation.

Elle ne décrit jamais son organisation interne.

Elle ne décrit jamais les technologies utilisées.

Le contrat constitue la frontière entre deux responsabilités.

---

# Pourquoi utiliser des contrats ?

Les contrats permettent aux composants d'évoluer indépendamment.

Tant que le contrat reste identique :

- l'implémentation peut évoluer ;
- les performances peuvent changer ;
- le langage peut être remplacé ;
- le système d'exploitation peut être différent.

Les autres composants continuent de fonctionner.

---

# Les différents types de contrats

Un contrat peut prendre plusieurs formes.

## Contrat réseau

Définition d'un protocole de communication.

Par exemple :

- morfBeacon
- HTTP
- UDP

---

## Contrat d'API

Définition des endpoints.

Des paramètres.

Des réponses.

Des erreurs.

---

## Contrat de format

Définition d'une structure JSON.

D'un fichier CSV.

D'un manifeste.

Ou d'un autre format d'échange.

---

## Contrat de comportement

Certains contrats ne concernent pas les données.

Ils décrivent un comportement attendu.

Par exemple :

- un service répond toujours à `/api/status` ;
- un service annonce sa présence via morfBeacon ;
- un service possède une version ;
- un service reste autonome.

---

# Un exemple concret : morfBeacon

Pour rendre ces notions tangibles, prenons le contrat le plus partagé de
l'écosystème : la découverte via morfBeacon.

Il réunit à lui seul les trois types précédents.

## La présence (contrat réseau + format)

Chaque service diffuse périodiquement, en UDP sur le réseau local, un petit
datagramme JSON qui annonce sa présence :

```json
{
  "proto": "morfbeacon/1",
  "app": "morfSensor",
  "host": "salon-pi",
  "version": "1.4.2",
  "state": "ok",
  "status_port": 8788,
  "instance": "morfSensor@salon-pi",
  "capabilities": ["sensors"],
  "ts": 1785000000
}
```

Le message est volontairement **court** : il est émis souvent.

## Le détail (contrat d'API)

Un consommateur intéressé interroge alors, à la demande, l'endpoint HTTP
`/status` du service, sur le port annoncé :

```json
{
  "app": "morfSensor",
  "version": "1.4.2",
  "state": "ok",
  "metrics": { "probes": 3 },
  "api": {
    "endpoints": [
      { "method": "GET", "path": "/api/readings", "summary": "dernières mesures" }
    ]
  }
}
```

Le détail peut être **riche** : il est lu rarement.

## Le comportement attendu

Le contrat impose enfin quelques règles de comportement :

- un service **annonce** sa présence tant qu'il fonctionne ;
- il **répond** à `/status` et à `/healthz` ;
- un consommateur **cherche une capacité** (`sensors`), jamais un nom : le nom
  peut être renommé par l'utilisateur, la capacité non.

## Ce que garantit ce contrat

Rien n'est dit de *comment* le service est écrit.

Un capteur en C++, un autre en Python ou un ESP32 peuvent tous l'honorer.

Tant que le format du datagramme et la sémantique de `/status` restent stables, un
superviseur écrit aujourd'hui continuera de fonctionner avec un service réécrit
demain.

C'est exactement ce que promet un contrat.

---

# Les contrats sont publics

Tous les contrats de morfSystem doivent être documentés.

Ils ne doivent jamais être déduits à partir du code.

Un développeur doit pouvoir implémenter un composant compatible uniquement en lisant la documentation.

---

# Les contrats évoluent lentement

Modifier un contrat est une décision importante.

Une évolution de contrat peut avoir un impact sur plusieurs projets.

Elle doit donc rester exceptionnelle.

Lorsque cela est possible, les contrats évoluent de manière compatible avec les versions précédentes.

---

# Les contrats avant le code

Lorsqu'une nouvelle interaction apparaît entre deux composants, le contrat est défini avant son implémentation.

Le code devient alors une traduction du contrat.

Jamais l'inverse.

---

# Les implémentations sont libres

Deux composants peuvent implémenter le même contrat de manière totalement différente.

Ils peuvent utiliser :

- des langages différents ;
- des bibliothèques différentes ;
- des plateformes différentes.

Du moment que le contrat est respecté, ces différences n'ont aucune importance.

---

# Les copies vendorées

Lorsqu'un contrat est partagé entre plusieurs plateformes incompatibles, une copie vendorée peut être utilisée.

Cette copie ne constitue pas une nouvelle référence.

Elle reste une projection du contrat officiel.

Le contrat demeure unique.

---

# Les contrats constituent le langage commun

Dans morfSystem, les composants ne partagent pas du code.

Ils partagent des contrats.

Ce choix garantit leur indépendance.

Il facilite également leur évolution.

Les composants changent.

Les contrats restent.

C'est cette stabilité qui donne sa cohérence à l'écosystème.