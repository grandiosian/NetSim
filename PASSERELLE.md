# Passerelle NetSim ↔ Centrale

Ce document est le contrat entre **la Centrale** (application centralisatrice : connexion des élèves, sauvegarde, progression, portail, parcours, suivi) et **NetSim** (application cible). Il résume les structures de données de NetSim, la façon dont elles sont gérées, et ce que chaque côté s’engage à respecter.

La version lisible par une machine est **[`centrale.json`](centrale.json)**, servi à l’adresse :

```
https://grandiosian.github.io/NetSim/centrale.json
```

Le même modèle de descripteur peut être publié par chaque application branchée sur la Centrale (Établi 3D, Argile, TechnoStudio…) : la Centrale n’a alors qu’une seule façon de lire les applications.

---

## 1. Le descripteur d’atelier (`passerelle.atelier` v1)

Un fichier JSON à la racine du site de l’application, nommé `centrale.json`.

| Section | Contenu | Utilisation par la Centrale |
|---|---|---|
| `format`, `version` | `"passerelle.atelier"`, `1` | Reconnaître le fichier. |
| `atelier` | `app`, `nom`, `description`, `url`, `urls_secours`, `icone`, `classes`, `version` | Remplir l’onglet **Ateliers** (mêmes champs que la table `ateliers`). |
| `centrale` | Module chargé, serveur par défaut, sites à autoriser, mode de connexion, fonctions du module utilisées | Savoir quoi autoriser dans **Réglages → Sites autorisés** et ce que l’application prend en charge (ouverture directe d’une activité, fichiers, dossier de projet…). |
| `activites` | Liste des activités, avec les champs envoyés (`id`, `titre`, `groupe`, `total`) et des champs descriptifs | Colonnes du **Suivi**, cartes du **portail**, étapes de **parcours** ; disponible avant même la première connexion d’un élève. |
| `progression` | Quand l’application envoie la progression, quels champs, et comment elle fusionne avec le serveur | Interpréter `faites`, `total`, `statut`. |
| `donnees` | Une entrée par clé passée à `Centrale.sauver` / `charger`, avec son schéma JSON | Afficher, valider ou exporter le travail de l’élève (page **Travail**, export ZIP). |
| `fichiers` | Fichiers et dossier de projet utilisés, ou `utilises: false` | Savoir si l’application peut être une étape « reçoit / produit » d’un atelier transversal. |
| `stockage_local` | Clés `localStorage` de l’application | Aide au dépannage sur un poste. |
| `acces_javascript` | Objet global exposé par l’application et ses fonctions | Point d’accès unique pour un module, un test ou une autre application. |
| `enums` | Valeurs possibles (niveaux, types d’appareils…) | Libellés. |
| `$defs` | Schémas JSON 2020-12 de toutes les structures | Validation. |

### Règles à respecter

**Côté application**
- Les `id` d’activité sont **stables** : on ne renomme jamais `e5-1`, on en ajoute d’autres. La progression des élèves y est rattachée.
- `Centrale.init({ activites })` envoie exactement les activités de `activites.liste`, dans le même ordre (champs `id`, `titre`, `groupe`, `total`).
- Chaque valeur passée à `Centrale.sauver(cle, valeur)` respecte le schéma déclaré dans `donnees.<cle>.format`.
- `atelier.version` est augmentée à chaque changement du descripteur.
- L’application fonctionne aussi sans la Centrale (le module est facultatif).

**Côté Centrale**
- L’application est identifiée par `atelier.app` (`netsim`).
- `faites` et `score` ne baissent jamais : le meilleur résultat est gardé (comportement actuel de `progression.php`).
- Une activité inconnue du descripteur est acceptée mais signalée (exercice ajouté depuis la dernière lecture).
- Les clés et champs inconnus sont ignorés : un descripteur plus récent ne casse pas une Centrale plus ancienne.

### Le catalogue des ateliers (`ateliers.json`)

À côté de `centrale.json`, NetSim publie **[`ateliers.json`](ateliers.json)** (format `catalogue.ateliers` v1 de la Centrale) : un exercice = un atelier, avec son niveau, son résumé, sa durée, ses compétences et son activité. Il est produit par `NetSim.ateliers()` et vérifié par `outils/verifier-descripteur.html`. La Centrale l’importe dans Ateliers → « Ateliers des applications ».

### Sans serveur

- **Ouvrir un exercice par son adresse** : `index.html#activite=e5-1` ouvre l’exercice, même sans la Centrale (l’avancement est gardé si l’élève y était déjà). Depuis le portail (`#centrale=…&activite=…`), c’est la connexion qui ouvre l’exercice demandé.
- **Sauvegarde de secours** : Fichier → Exporter le schéma → « Enregistrer le fichier » (`5A-07-e5-1.json` si l’élève est connecté, sinon `netsim-e5-1.json`) et « Déposer sur Nuage », qui ouvre le dossier Nuage de la classe : lien de la Centrale si l’élève est connecté, sinon lien collé par le professeur sur le poste (clé locale `netsim-nuage`).

Règles communes : [Données, bonnes pratiques et compatibilité](https://technoversailles.alwaysdata.net/passerelle/?doc=donnees).

---

## 2. Les données de NetSim

### Vue d’ensemble

```
                    ┌──────────────── NetSim (navigateur de l’élève) ────────────────┐
                    │                                                                 │
  Fichier ▸ Exporter│   S  (schéma en cours)  ──► localStorage « netsim-v1 »          │
  Fichier ▸ Importer│        │                                                        │
                    │        ├─ devices[]  appareils (PC, switch, routeur…)           │
                    │        ├─ cables[]   liaisons port à port                       │
                    │        ├─ zones[]    salles / sous-réseaux                      │
                    │        └─ exo        consigne de l’exercice en cours            │
                    │                                                                 │
                    │   PROG (progression) ──► localStorage « netsim-progress »       │
                    │        ├─ <id exercice> : n, best, started, done                │
                    │        └─ __seen : découvertes (types d’actions déjà faites)    │
                    │                                                                 │
                    │   window.NetSim  ◄── point d’accès unique                       │
                    └───────────────┬─────────────────────────────────────────────────┘
                                    │ centrale-client.js (si connecté)
          sauver("schema")  ────────┤ charger("schema")
          sauver("decouvertes") ────┤ charger("decouvertes")
          progression(id, …)  ──────┤ mesProgressions()
          init({activites}) ────────┤ activiteDemandee()
                                    ▼
                               Centrale (alwaysdata)
```

### Le schéma réseau (`schema`)

C’est l’objet `S` de l’application, enregistré sous la clé `schema`. Exemple réduit :

```json
{
  "level": 1,
  "help": true,
  "nextId": 24,
  "devices": [
    { "id": 1, "type": "pc", "name": "PC 1", "x": 96, "y": 96,
      "ip": "192.168.1.11", "mask": "255.255.255.0", "gw": "192.168.1.254",
      "mac": "02:4C:00:01:00:2C", "web": { "on": false, "page": "Bienvenue…" } },
    { "id": 8, "type": "switch", "name": "Switch 1", "x": 260, "y": 232 },
    { "id": 9, "type": "router", "name": "Routeur 1", "x": 640, "y": 232,
      "ifs": [ { "ip": "192.168.1.254", "mask": "255.255.255.0", "mac": "02:4C:00:09:00:0D" },
               { "ip": "192.168.2.254", "mask": "255.255.255.0", "mac": "02:4C:00:09:01:18" },
               { "ip": "", "mask": "255.255.255.0", "mac": "…" }, { "ip": "", "mask": "255.255.255.0", "mac": "…" } ],
      "routes": [] }
  ],
  "cables": [ { "id": 12, "a": { "d": 1, "p": 0 }, "b": { "d": 8, "p": 0 } } ],
  "zones":  [ { "id": 3, "name": "Salle 101", "x": 0, "y": 0, "w": 520, "h": 520, "color": "bleu" } ],
  "exo": { "k": "e4-1", "c": "4e", "cat": "routeur", "t": "Relier deux salles", "dur": "40 min",
           "steps": ["Avec l’outil Câble, relie…", "…"], "q": ["…"], "verif": "…", "cur": 2, "done": [true, true] }
}
```

| Champ | Description |
|---|---|
| `devices[].type` | `pc`, `laptop`, `phone`, `rpi`, `server`, `printer`, `esp32`, `switch`, `router`. |
| `ip`, `mask`, `gw`, `mac` | Postes uniquement. Chaîne vide = non configuré. |
| `web` | Serveur web de l’appareil : `on`, et `page` (1re ligne = titre). |
| `sensor` | ESP32 : capteur de température publié en page web. |
| `ifs` | Routeur : 4 ports `eth0` à `eth3`, chacun avec `ip`, `mask`, `mac`. |
| `routes` | Routeur : routes statiques `{net, mask, via}`. |
| `cables[]` | Relie le port `p` de l’appareil `d` (`a`) au port `p` d’un autre (`b`). Un port ne porte qu’un câble. |
| `zones[]` | Salles dessinées ; le sous-réseau affiché est calculé, il n’est pas stocké. |
| `nextId` | Compteur partagé par appareils, câbles et salles ; recalculé à l’import. |
| `level` | 0 = 5ᵉ Débutant, 1 = 4ᵉ Intermédiaire, 2 = 3ᵉ Avancé. |
| `help` | Alertes de configuration affichées (désactivées pour la recherche de panne). |
| `exo` | Présent seulement pendant un exercice : la consigne et l’avancement voyagent avec le schéma. |

Ce qui **n’est pas** dans le schéma, car recalculé à chaque simulation : tables ARP et MAC, trames en cours, journal des trames, temps simulé.

### La progression

Dans NetSim (`PROG`, clé locale `netsim-progress`) :

```json
{ "e5-1": { "n": 5, "best": 5, "started": true, "done": true },
  "e4-1": { "n": 8, "best": 3, "started": true },
  "__seen": ["cable", "ipfield", "play", "terminal"] }
```

Envoyée à la Centrale par `Centrale.progression(id, NetSim.progression(id))` :

```json
{ "titre": "Relier deux salles", "faites": 3, "total": 8, "statut": "commence" }
```

| NetSim | Centrale | Règle |
|---|---|---|
| `best` | `faites` | Meilleur nombre d’étapes validées, jamais diminué. |
| `n` | `total` | Nombre d’étapes de l’exercice. |
| `done` | `statut: "termine"` | Sinon `"commence"`. |
| `__seen` | clé de données `decouvertes` | Types d’actions déjà faites ; l’aide « Comment faire » se réduit d’autant. |

### Les compétences

NetSim s’appuie sur le référentiel de la Centrale (`/passerelle/competences.json`).

- **Déclaration** : chaque exercice porte `competences` dans `NetSim.activites()` (table `COMP_EXO` de `index.html`, recopiée dans `activites.liste`). 5e → `SFC15.5.1` (composants, topologie) ou `SFC15.5.2` (adresses) ; 4e → `SFC15.4.1/2/3`, `CCRI17.4.1` pour le capteur connecté ; 3e → `SFC15.3.1/2`, `CCRI22.3.1` pour le projet testé par un camarade.
- **Constats** : quand la dernière étape est validée, la progression envoyée contient `constats` (une fois par essai, `NetSim.constats(id)`).

```json
{ "titre": "Brancher la salle", "faites": 5, "total": 5, "statut": "termine",
  "constats": [{ "competence": "SFC15.5.1", "reussi": true, "indices": 3, "essais": 2,
                 "preuve": "Exercice « Brancher la salle » : 5 étapes, 1 indice ouvert, 2 étapes passées" }] }
```

| Champ | Ce que NetSim compte (dans `S.exo`) |
|---|---|
| `indices` | Étapes où « Besoin d’un indice ? » a été ouvert (`ind`) + étapes automatiques passées avec « Passer cette étape » (`saut`). |
| `essais` | `essai` : 1 + nombre de « Recommencer » avant de terminer. |
| `reussi` | Faux si plus de la moitié des étapes automatiques ont été passées. |

La Centrale en déduit le niveau (pas réussi → 1 ; sans aide du premier coup → 4 ; au plus 1 aide et 3 essais → 3 ; sinon 2) et fait le bilan en moyenne haute avec les notes du professeur.

### Quand les données circulent

| Moment | NetSim fait |
|---|---|
| Chargement de la page | Lit `netsim-v1` et `netsim-progress` ; charge le module de la Centrale (sans effet s’il est injoignable). |
| Connexion (code ou jeton `#centrale=`) | `mesProgressions()` → fusion (le meilleur gagne) → renvoie ce qui est meilleur localement. `charger("decouvertes")` → réunion. `charger("schema")` → remplace le schéma local s’il diffère (Annuler pour revenir), sinon `sauver("schema")`. Puis ouvre `activiteDemandee()` si l’élève n’y est pas déjà. |
| Chaque modification du schéma | `netsim-v1` après 300 ms, puis `sauver("schema")` (regroupé par la Centrale). |
| Ouverture d’un exercice, étape validée, exercice terminé | `progression(id, …)` (avec `constats` à la fin de l’exercice) et `sauver("decouvertes")`. |
| Déconnexion | Le module envoie ce qui reste ; NetSim continue en local. |

---

## 3. Point d’accès JavaScript

`window.NetSim` est le seul endroit où le module de la Centrale lit les données de NetSim. Un test, un autre module ou une autre application peut s’en servir de la même façon.

| Membre | Rôle |
|---|---|
| `NetSim.app`, `NetSim.version` | `"netsim"` et la version (identique à `centrale.json`). |
| `NetSim.activites()` | Liste envoyée à `Centrale.init`. |
| `NetSim.ateliers()` | Contenu de `ateliers.json` (catalogue des ateliers). |
| `NetSim.catalogue()` | Catalogue détaillé : classe, niveau, catégorie, résumé, durée, étapes, étapes automatiques, questions. |
| `NetSim.progression(id)` | `{titre, faites, total, statut}` ou `null`. |
| `NetSim.constats(id)` | Constats de compétences si l’exercice en cours vient d’être terminé (une seule fois), sinon `[]`. |
| `NetSim.fusionnerProgression(serveur)` | Fusionne le résultat de `Centrale.mesProgressions()`. |
| `NetSim.donnees()` | `{schema, decouvertes}` : valeurs des clés enregistrées. |
| `NetSim.estSchema(objet)` | Vérifie qu’un objet est un schéma chargeable. |

---

## 4. Faire évoluer NetSim sans casser la Centrale

1. Ajouter un exercice : nouvel `id`, jamais réutilisé, et ses capacités dans `COMP_EXO`.
2. Changer `NETSIM_VERSION` dans la page et `atelier.version` dans `centrale.json`.
3. Ouvrir [`outils/verifier-descripteur.html`](outils/verifier-descripteur.html) : la page compare `centrale.json` et `ateliers.json` avec l’application et donne leur contenu à jour en cas d’écart.
4. Changer la forme d’une donnée enregistrée : mettre à jour son schéma dans `$defs`, et garder la lecture de l’ancienne forme (les élèves ont du travail déjà enregistré).

## 5. Pistes pour la suite

- **Centrale** : lire `centrale.json` depuis l’onglet Ateliers (remplissage automatique de la fiche et des activités), et valider les données reçues avec les schémas.
- **NetSim, ateliers transversaux** : recevoir un schéma de départ depuis le dossier de projet (`reçoit : reseau.json`) et y déposer le réseau terminé (`produit : reseau.json`), avec `Centrale.etape()` et `Centrale.projet`.
