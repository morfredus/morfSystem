# Contrat d'activité en cours (`activity/1`)

Ce document définit un contrat **générique** : comment un service morfSystem
déclare **ce qu'il est en train de faire**, distinct de son simple état.

Il complète le contrat morfBeacon (présence + capacités) et le contrat `/status`
(détail du service). Voir [CONTRACTS.md](CONTRACTS.md).

---

## 1. Le besoin

Un superviseur (morfMonitor) sait déjà **qu'un service tourne** et **combien il
consomme**. Il ne sait pas **expliquer** cette consommation : quand morfPhoto
indexe, morfMonitor voit 25-30 % de CPU sans pouvoir dire qu'une indexation est
en cours.

Chaque service capable d'un **travail long ou identifiable** doit donc pouvoir
annoncer son **activité courante** : nature, état, début, progression éventuelle,
quantité traitée / totale, détail utile.

## 2. Ce que le contrat n'est pas

- **Il n'est pas transporté dans le heartbeat morfBeacon.** Le beacon reste
  réservé à la présence et aux capacités : « je suis là, voici ce que je sais
  faire et comment me joindre ». L'activité est une donnée métier plus volatile.
- **Il ne décrit pas l'historique.** Le temps réel appartient à morfMonitor ; la
  mémoire de ce qui s'est passé appartient à morfAnalytics (voir §6).
- **Il ne permet aucun contrôle.** Un consommateur observe une activité, il ne la
  démarre ni ne l'arrête (principe « observer plutôt que contrôler »).

## 3. Où : le champ `activity` dans `/status`

Un service qui a une activité en cours ajoute un objet **optionnel** `activity`
à sa réponse `GET /status`. morfMonitor interroge déjà `/status` à chaque
heartbeat : aucun endpoint ni polling supplémentaire n'est requis.

```json
{
  "app": "morfPhoto",
  "version": "0.3.0",
  "state": "ok",
  "metrics": { "photos": 16043 },
  "activity": {
    "type": "indexation",
    "state": "running",
    "started_at": 1787594100,
    "current": 8421,
    "total": 16043,
    "progress_percent": 52.5,
    "detail": "Photos/2007/..."
  }
}
```

### Champs

| Champ | Type | Requis | Sens |
|---|---|---|---|
| `type` | string | oui | genre de travail, court et stable (`indexation`, `compilation`, `collecte`, `synchronisation`, `sauvegarde`...). Minuscules, `snake_case`. |
| `state` | string | oui | `running` (en cours) ou `idle` (rien en cours). |
| `started_at` | entier (epoch s) | non | début de l'activité. |
| `current` | nombre | non | quantité déjà traitée. |
| `total` | nombre | non | quantité totale à traiter (si connue). |
| `progress_percent` | nombre 0-100 | non | avancement ; à défaut, déductible de `current`/`total`. |
| `detail` | string | non | élément en cours (dossier, fichier, cible...), court. |

### Règles

- **Optionnel et additif.** L'absence du champ `activity`, ou `state: "idle"`,
  signifie « rien en cours ». Un service qui ne fait jamais de travail long peut
  ne jamais l'émettre.
- **Une activité courante par service** (objet unique). Si un service doit un
  jour en exposer plusieurs simultanément, ce sera une évolution explicite du
  contrat (`activity/2`, tableau), pas un changement silencieux.
- **`snake_case`, UTF-8.**
- **Compatible.** Ajouter des champs optionnels reste dans `activity/1`. Retirer
  ou renommer un champ existant impose une nouvelle version.

## 4. Le consommateur : morfMonitor (temps réel)

morfMonitor lit `activity` dans les `/status` qu'il collecte déjà et affiche une
section générique **« Activités en cours »**, non spécifique à un service :

```
Activités en cours

SERVICE      ACTIVITÉ       PROGRESSION       DURÉE       DÉTAIL
morfPhoto    Indexation     8 421 / 16 043    12 min      Photos/2007/...
```

Une même ligne représente aussi bien une indexation morfPhoto qu'une compilation
morfDeploy, une collecte morfCollector ou une synchronisation morfSync. Si aucune
activité n'est en cours, la section l'indique simplement.

morfMonitor **observe** : il n'agit jamais sur l'activité.

## 5. La découverte reste par capacité

Un service reste trouvé par sa **capacité** annoncée dans le beacon, jamais par
son nom. Le champ `activity` ne change rien à la découverte : il enrichit le
`/status` d'un service déjà repéré.

## 6. La frontière avec morfAnalytics (historique)

morfMonitor décrit le **présent** (« morfPhoto indexe, 52 % »). morfAnalytics
conserve le **passé** (durée totale, CPU consommé, température max, résultat).

À la **fin** d'une activité, le service **propriétaire** la déclare à un service
annonçant la capacité `analytics` (morfAnalytics), via le mécanisme historique
déjà en place :

```
POST /api/monitor/activity
Content-Type: application/json

{
  "type": "indexation",
  "project": "morfPhoto",
  "machine": "pi-salon",
  "start_ts": 1787594100,
  "end_ts": 1787594820,
  "status": "success",
  "metadata": { "files": 16043, "folders": 512, "errors": 3 }
}
```

- Best-effort et **optionnel** : si aucun service `analytics` n'est joignable,
  l'activité se termine normalement, sans erreur bloquante (le propriétaire
  journalise l'échec d'envoi, sans plus).
- C'est le **propriétaire** de l'activité qui déclare sa fin (comme morfDeploy
  pour une compilation). morfMonitor n'a pas ce rôle : il observe le présent, il
  ne recopie pas l'historique.
- Le temps réel (`/status`) et l'historique (`POST`) ne se mélangent jamais.

## 7. Résumé

- Un service ne dit plus seulement **qu'il travaille** ; quand il le sait, il dit
  **ce qu'il fait**, via `activity` dans `/status`.
- morfMonitor l'**affiche en temps réel**.
- morfAnalytics le **conserve** ensuite comme activité historique, sur
  déclaration du propriétaire.
