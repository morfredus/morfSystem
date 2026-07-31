# Feuille de route

La feuille de route de morfSystem ne constitue pas une liste de fonctionnalités.

Elle décrit les grandes orientations de l'écosystème.

Les priorités peuvent évoluer.

Les principes, eux, restent stables.

---

# Les objectifs

L'objectif de morfSystem n'est pas de multiplier les composants.

L'objectif est de construire un écosystème cohérent de services autonomes capables de collaborer simplement.

Chaque évolution doit renforcer cette cohérence.

---

# Priorité n°1
## Stabiliser

Avant d'ajouter de nouvelles fonctionnalités, les composants existants doivent atteindre un niveau élevé de stabilité.

Cela implique notamment :

- la consolidation des contrats ;
- la simplification des implémentations ;
- l'amélioration des diagnostics ;
- la documentation des comportements.

Une architecture stable constitue la meilleure base pour les évolutions futures.

---

# Priorité n°2
## Améliorer l'expérience d'installation

Un nouvel utilisateur doit pouvoir découvrir morfSystem rapidement.

L'installation doit devenir :

- simple ;
- reproductible ;
- documentée ;
- indépendante de la plateforme lorsque cela est possible.

L'objectif est qu'un premier service puisse être opérationnel en quelques minutes.

---

# Priorité n°3
## Consolider les contrats

Les contrats représentent le langage commun de l'écosystème.

Ils doivent être :

- documentés ;
- versionnés ;
- compatibles ;
- stables.

Chaque nouveau contrat doit être conçu avant son implémentation.

---

# Priorité n°4
## Développer les services

De nouveaux composants apparaîtront naturellement.

Ils devront toujours répondre à une responsabilité clairement identifiée.

Leur création ne constitue jamais un objectif en soi.

---

# Priorité n°5
## Renforcer la supervision

L'observation de l'écosystème constitue un axe important de développement.

Les outils de supervision devront permettre de comprendre rapidement :

- l'état des services ;
- leur santé ;
- leurs interactions ;
- les éventuelles anomalies.

---

# Priorité n°6
## Faire de morfAnalytics le moteur d'analyse et d'interprétation de l'écosystème

morfAnalytics ne doit pas être réduit à son premier cas d'usage, MeteoHub.

Son rôle est de travailler sur une copie locale et reconstruisible d'un
historique, puis d'en produire des résultats synthétiques, compréhensibles et
actionnables. La source conserve toujours la vérité de ses données ; le moteur
d'analyse ne modifie jamais l'équipement ou le service observé.

Les nouveaux domaines seront ajoutés sous forme de jeux d'analyses enfichables.
Le moteur commun ne doit connaître ni la météo, ni le réseau, ni les journaux
Web : il reçoit un contrat de données documenté et produit des résultats
versionnés.

### Étape 1 — Contrat commun et restitution

Avant de connecter une nouvelle source, l'écosystème doit définir :

- le format minimal des événements et des séries reçus ;
- les identifiants stables, les horodatages et les règles de rétention ;
- les indicateurs synthétiques attendus et leurs limites d'interprétation ;
- une restitution Web lisible, avec une synthèse humaine avant les détails ;
- le comportement lorsque l'historique est insuffisant : une progression
  d'apprentissage, pas une conclusion fragile.

Chaque analyse doit pouvoir expliquer la période étudiée, la comparaison de
référence et la raison d'une alerte. Une anomalie est un signal à examiner,
jamais une affirmation de cause.

### Étape 2 — Analyse réseau avec GatewayLab

GatewayLab constitue le prochain domaine naturel : il connaît les équipements
vus sur le réseau et les événements de présence. morfAnalytics devra en tirer
une page de compréhension du réseau, sans se substituer à GatewayLab pour le
scan, l'inventaire ou les décisions immédiates.

Les analyses visées sont :

- stabilité globale du réseau et évolution de ce score ;
- nombre moyen d'équipements présents, avec variation par rapport aux périodes
  comparables ;
- nouveaux appareils détectés et appareils rarement ou fréquemment observés ;
- durées de présence, retours et pertes d'équipements ;
- périodes habituelles d'activité, par heure et par jour ;
- changements d'adresse IP d'un même équipement, en distinguant le DHCP normal
  d'un comportement à examiner ;
- anomalies de présence ou de rythme de connexion ;
- évolution des fabricants représentés sur le réseau.

La synthèse doit pouvoir répondre clairement à des questions simples :
« le réseau a-t-il été stable ? », « qu'est-ce qui est nouveau ? », « quels
équipements ont disparu ou reviennent de manière inhabituelle ? ».

### Étape 3 — Analyse de journaux Web avec SiteWatch

SiteWatch fournit un second domaine : les événements issus des journaux HTTP.
morfAnalytics doit les replacer dans leur contexte temporel et dégager les
changements significatifs, sans devenir un outil de mesure d'audience ou de
sécurité intrusive.

Les analyses visées sont :

- évolution des réponses HTTP en erreur, avec les pages les plus touchées ;
- comparaison avec les jours et périodes précédents ;
- détection de créneaux anormaux et de répétitions ;
- robots les plus actifs et nouvelles signatures observées ;
- répétition des tentatives sur les points sensibles, notamment `wp-login` et
  XML-RPC ;
- influence visible ou non sur la disponibilité du site ;
- synthèse rédigée à partir de règles explicables, par exemple : « Activité
  globalement normale, avec une hausse ponctuelle des requêtes automatisées
  entre 2 h et 4 h. Aucun impact visible sur la disponibilité du site. »

Les conclusions doivent distinguer les faits observés, les comparaisons et les
limites : une hausse de robots n'établit pas, à elle seule, une attaque ni un
impact utilisateur.

### Étape 4 — Rapports et diffusion optionnelle

Une fois les contrats et les analyses stabilisés, morfAnalytics pourra produire
des synthèses périodiques par source (réseau, site, météo), consultables dans sa
page Web et publiables vers morfSync. Cette diffusion restera à sens unique :
un résultat d'analyse peut être partagé, mais la donnée source reste souveraine.

---

# Priorité n°7
## Améliorer la gouvernance

À mesure que l'écosystème grandit, ses conventions doivent rester simples.

La gouvernance vise à préserver :

- la cohérence ;
- la lisibilité ;
- la stabilité ;
- l'autonomie des projets.

---

# Les priorités permanentes

Certaines orientations ne sont jamais considérées comme terminées.

Par exemple :

- simplifier le code ;
- améliorer la documentation ;
- réduire les dépendances ;
- améliorer les diagnostics ;
- clarifier les responsabilités.

Ces travaux accompagnent l'ensemble des projets.

---

# Ce qui n'est pas une priorité

La feuille de route ne cherche pas :

- à suivre les modes technologiques ;
- à intégrer systématiquement les nouvelles bibliothèques ;
- à augmenter artificiellement le nombre de fonctionnalités.

Toute évolution doit répondre à un besoin clairement identifié.

---

# Une évolution continue

morfSystem n'a pas vocation à être terminé.

L'écosystème continuera d'évoluer.

Chaque évolution devra cependant respecter les principes fondateurs qui lui donnent son identité.

La feuille de route constitue une direction.

Elle ne remplace jamais les principes d'architecture.
