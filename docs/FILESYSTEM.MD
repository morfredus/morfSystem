# Gestion des données locales

L'ensemble des services morfSystem stocke ses fichiers selon une organisation
commune. Cette convention permet à un administrateur de retrouver immédiatement la
nature des données d'un service, quelle que soit son implémentation.

L'objectif n'est pas d'imposer une arborescence arbitraire, mais de distinguer
clairement les données qui constituent la responsabilité du service de celles qui
peuvent être reconstruites.

---

# Principe général

Chaque service est installé dans son propre répertoire sous `/opt`.

Lorsqu'un service persiste des informations sur le disque, celles-ci sont
réparties selon leur rôle.

```
/opt/<service>/
├── <exécutable>
├── config/
├── data/
├── cache/
├── logs/
└── tmp/
```

Tous les répertoires ne sont pas obligatoires. Un service ne crée que ceux dont il
a réellement besoin.

Les applications clientes ne possèdent pas de répertoire dédié sous `/opt`. Elles
utilisent les services morfSystem, qui demeurent propriétaires des données
persistantes.

---

## `config/`

Le répertoire `config` contient la configuration persistante du service.

Ces fichiers décrivent le comportement du service mais ne constituent pas ses
données métier.

Ils doivent être sauvegardés.

Exemples :

- paramètres utilisateur ;
- configuration réseau ;
- manifestes locaux ;
- fichiers JSON de configuration.

---

## `data/`

Le répertoire `data` contient les données dont le service est propriétaire.

Il s'agit de la source de vérité du service.

La suppression de ce répertoire entraîne une perte définitive d'information.

Les données présentes ici doivent être sauvegardées.

Exemples :

- bases de données métier ;
- fichiers collectés ;
- documents synchronisés ;
- états persistants ;
- curseurs de traitement ;
- manifestes ;
- archives.

---

## `cache/`

Le répertoire `cache` contient des données locales pouvant être entièrement
reconstruites.

Leur suppression ne provoque aucune perte fonctionnelle durable.

Le service doit être capable de régénérer automatiquement son cache.

Ce répertoire n'a pas vocation à être sauvegardé.

Exemples :

- cache SQLite ;
- index de recherche ;
- miniatures ;
- résultats de calcul ;
- données importées depuis une autre source de vérité.

---

## `logs/`

Le répertoire `logs` contient les journaux produits par le service lui-même.

Ces fichiers servent au diagnostic, au suivi de fonctionnement et à l'exploitation.

Ils peuvent être supprimés ou archivés sans affecter le fonctionnement du service.

Les données collectées depuis un système externe ne relèvent pas de `logs`, mais
de `data`, puisqu'elles deviennent des données métier du service.

---

## `tmp/`

Le répertoire `tmp` contient les fichiers temporaires utilisés pendant l'exécution.

Son contenu peut être supprimé à tout moment.

Le service ne doit jamais supposer que ces fichiers survivront à un redémarrage.

---

# Règle de décision

Lorsqu'un développeur hésite entre `data` et `cache`, une seule question doit être
posée :

> Si ce répertoire disparaît complètement, le service peut-il reconstruire
> automatiquement son contenu sans perdre d'information ?

Si la réponse est oui, il s'agit d'un cache.

Si la réponse est non, il s'agit de données métier (`data`).

Cette règle prévaut sur la nature technique des fichiers. Une base SQLite peut
aussi bien être une base métier (`data`) qu'un simple cache (`cache`) selon son
rôle dans l'architecture.

---

# Principe architectural

Dans morfSystem, les services sont propriétaires des données.

Une application cliente ne possède pas de données métier. Elle consomme les
services du système et leur délègue la persistance.

Cette séparation garantit que :

- les données restent disponibles même si un client est remplacé ;
- plusieurs clients peuvent accéder simultanément au même service ;
- la sauvegarde s'effectue au niveau des services, jamais des interfaces
  utilisateur ;
- chaque responsabilité reste clairement identifiée.

Cette règle participe directement au principe fondateur de morfSystem :
un service = une responsabilité = un propriétaire des données.
