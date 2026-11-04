# NetSim

Simulateur de réseau pour le collège, le lycée et le supérieur, dans l’esprit de Filius. Il fonctionne directement dans le navigateur.

**Ouvrir l’application : https://grandiosian.github.io/NetSim/**

Aucune installation : une seule page HTML (`index.html`). Le travail de chaque élève est enregistré dans son navigateur et peut être exporté ou importé sous forme de fichier `.json`.

## Ce qu’on peut faire

- **Concevoir** un réseau : PC, portable, smartphone, Raspberry Pi, serveur, imprimante, ESP32, switch et routeur ; câblage ; adresses IP, masque et passerelle ; salles et sous-réseaux. Sélection multiple : Maj + clic, Ctrl + glisser sur le fond pour un rectangle, puis déplacement du groupe.
- **Simuler** : chaque appareil ouvre son écran (bureau, invite de commandes avec `ping`, `ipconfig`, `tracert`, `arp`, `route`, `nslookup`, `imprimer`), un navigateur web, un serveur web modifiable, une messagerie et une file d’impression.
- **Filtrer** : chaque routeur a un pare-feu (politique par défaut, règles lues dans l’ordre par service, source et destination). Seules les demandes sont filtrées : les réponses passent, comme sur un pare-feu à états.
- **Voir les données circuler** : enveloppes animées sur les câbles, tiroir « Trames échangées » avec le détail de l’encapsulation (Ethernet, IP, TCP, HTTP).

## Classes et niveaux de difficulté

Le choix de classe se fait avec **Changer de classe**, dans le widget « Visiteur » de la barre du haut. Il y a trois onglets :

| Onglet | Classes | Ateliers |
|---|---|---|
| Collège | 5ème, 4ème, 3ème | 31 |
| Lycée | 2nde, 1re, Terminale | 9 |
| Supérieur | Bac+1, Bac+2 | 12 |

Chaque classe propose des ateliers aux niveaux **Débutant**, **Intermédiaire** et **Avancé**. Au collège, le niveau de l’atelier suit sa classe (5ème : Débutant, 4ème : Intermédiaire, 3ème : Avancé). Pastilles : vert, jaune et rouge pour le collège, turquoise, cyan et magenta pour le lycée, rose et rose-rouge pour le supérieur.

Le choix de classe ne change pas la vue : depuis le menu, on reste dans le menu ; depuis la création, on reste en création. Quand un atelier est en cours ou qu’un document est ouvert, changer de classe ouvre l’atelier le plus proche du niveau choisi.

## Ateliers (pas à pas)

Les ateliers se trouvent dans le menu **Mes ateliers** (bouton de la barre du haut). Ils sont présentés comme un parcours, classe par classe, avec leur progression ; un atelier se débloque quand le précédent est terminé. « Effacer ma progression » est en bas du menu.

Le panneau **Atelier courant** affiche l’atelier en cours, ou le prochain de la classe et du niveau choisis.

Le panneau **Consigne** guide l’élève une étape à la fois : une barre de progression, un rappel, et la plupart des étapes se valident toutes seules quand l’action est faite (câble posé, adresse correcte, ping réussi, page affichée…). À la fin, les questions et le critère de vérification s’affichent.

Thèmes principaux :
- **Collège 5ème** : premiers pas, câbles, adresses IP, services (impression, page web).
- **Collège 4ème** : réseau de salle, routeur et passerelle, ARP et table du switch, diagnostic, capteur connecté, DHCP, noms de réseau.
- **Collège 3ème** : sous-réseaux et masques, routage (routes statiques, TTL), messagerie, DNS, DHCP, encapsulation.
- **Lycée** : 2nde (réseau local, table du switch, doublon) ; 1re (passerelle, masque, deux routeurs en chaîne) ; Terminale (page web, DNS, DHCP).
- **Supérieur** (deux ateliers par niveau de difficulté ; les consignes donnent l’objectif, pas la valeur à recopier) :
  - Bac+1 : serveur de messagerie ; ARP, passerelle et TTL ; découpage en sous-réseaux ; le ping à sens unique (masque incohérent) ; une route fausse localisée avec `tracert` ; route par défaut, agrégation et boucle de routage.
  - Bac+2 : plan d’adressage VLSM ; dépannage DNS avec `nslookup` ; DHCP qui annonce passerelle et DNS ; pare-feu (filtrage) ; réseau multi-sites en triangle avec reroutage après une panne ; DMZ.

Au lycée et dans le supérieur, tous les outils de la simulation sont disponibles quelle que soit la difficulté de l’atelier (masque, routes, DNS, messagerie, pare-feu).

## Mes documents

Le menu **Mes ateliers** affiche aussi **Mes documents** : les schémas libres de l’élève. Chaque document est enregistré automatiquement dans le navigateur.

- **+ Nouveau document**, **Importer…**, **Tout exporter** (un fichier `netsim-documents.json`).
- **Ouvrir**, **Exporter**, **Supprimer** sur chaque document ; double-clic sur le nom pour le renommer sur place.
- Le nom du document en cours apparaît dans la barre du haut.

## Démos

Menu **Fichier → Démos…** :

- **Réseaux d’exemple** : une salle, deux salles et un routeur, un anneau, un maillage.
- **Démo guidée** : « Mon premier câble », jouée avec un pointeur à l’écran (glisser, relier, saisir les adresses, ping).
- **Démos en boucle** (pour une porte ouverte) : ping dans la salle, plusieurs appareils, le paquet qui traverse le routeur, le tour de l’anneau, le maillage. Elles tournent jusqu’à **Arrêter la démo**.

Le document créé par une démo est supprimé à la fin.

## Simplifications

Le smartphone et l’ESP32 sont reliés par une liaison Wi-Fi dessinée en pointillés. Les ateliers du collège ne montrent que les protocoles à leur niveau (pas de poignée de main TCP à l’écran).

## Connexion à la Centrale de la classe (facultatif)

Le widget **Visiteur**, dans la barre du haut, permet de se connecter avec son code à la Centrale de l’enseignant (`technoversailles.alwaysdata.net`) :

- le schéma en cours est enregistré sur le serveur et retrouvé sur n’importe quel ordinateur ;
- l’avancement de chaque atelier (étapes validées, atelier terminé) remonte dans le tableau de suivi de l’enseignant ;
- une fois connecté, la classe est donnée par le code.

Sans connexion ou sans réseau, rien ne change : NetSim enregistre dans le navigateur.

**Sans serveur** : chaque atelier s’ouvre directement par son adresse, par exemple `https://grandiosian.github.io/NetSim/#activite=e5-1`. **Fichier → Exporter le schéma** propose « Enregistrer le fichier » et « Déposer sur Nuage » : le fichier est enregistré puis le dossier Nuage de la classe s’ouvre. C’est la sauvegarde de secours.
Pour utiliser une autre Centrale, définir `window.CENTRALE_URL = "https://…/"` avant le chargement de la page.

### Passerelle avec la Centrale

Le contrat entre NetSim et la Centrale (structures de données, clés enregistrées, activités, progression) est décrit dans [`PASSERELLE.md`](PASSERELLE.md). Sa version lisible par une machine est [`centrale.json`](centrale.json) : fiche de l’atelier, liste des 52 activités et schémas JSON des données. Le catalogue [`ateliers.json`](ateliers.json) liste les 52 ateliers avec leurs compétences, importé par la Centrale (Ateliers → « Ateliers des applications »). La page [`outils/verifier-descripteur.html`](outils/verifier-descripteur.html) vérifie que ces deux fichiers correspondent à l’application et donne leur version à jour en cas d’écart.

**Règle :** toute modification d’un atelier (titre, résumé, nombre d’étapes, catégorie, classe, niveau) doit être reportée dans `ateliers.json` et `centrale.json`, puis vérifiée avec le verifier.

Règles communes à toutes les applications : [Données, bonnes pratiques et compatibilité](https://technoversailles.alwaysdata.net/passerelle/?doc=donnees) et [Compétences](https://technoversailles.alwaysdata.net/passerelle/?doc=competences).
