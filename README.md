# NetSim

Simulateur de réseau pour le collège (5ᵉ, 4ᵉ, 3ᵉ), dans l’esprit de Filius, qui fonctionne directement dans le navigateur.

**Ouvrir l’application : https://grandiosian.github.io/NetSim/**

Aucune installation : une seule page HTML. Le travail de chaque élève est enregistré dans son navigateur et peut être exporté ou importé sous forme de fichier `.json`.

## Ce qu’on peut faire

- **Concevoir** un réseau : PC, portable, smartphone, Raspberry Pi, serveur, imprimante, ESP32, switch et routeur ; câblage ; adresses IP, masque et passerelle ; salles et sous-réseaux.
- **Simuler** : chaque appareil démarre un petit système (bureau, barre des tâches) avec une invite de commandes (`ping`, `ipconfig`, `tracert`, `arp`, `route`, `imprimer`), un navigateur web, un serveur web modifiable, une file d’impression et un capteur de température pour l’ESP32.
- **Voir les données circuler** : enveloppes animées sur les câbles (ARP, ping, web, erreurs), coches vertes et croix rouges sur les appareils, tiroir des trames avec le détail de l’encapsulation (Ethernet, IP, TCP, HTTP).

## Trois niveaux

| Niveau | Classe | Ce qui est visible |
|---|---|---|
| Débutant | 5ᵉ | Adresses IP seulement, ping, navigateur, impression. Pas d’ARP à l’écran, messages en phrases simples. |
| Intermédiaire | 4ᵉ | Passerelle, routeur, adresses MAC, ARP, `tracert`. Le masque reste fixé à 255.255.255.0. |
| Avancé | 3ᵉ | Masque de sous-réseau, routes statiques, ports TCP, toutes les tables. |

## Exercices intégrés (pas à pas)

Menu **Fichier → Exemples et exercices**, rangés par classe puis par thème. Chaque exercice s’ouvre au bon niveau avec son schéma de départ. Le panneau **Consigne** guide l’élève une étape à la fois : une barre de progression, un indice à déplier, et la plupart des étapes se valident toutes seules quand l’action est faite (câble posé, adresse correcte, ping réussi, page affichée…). À la fin, les questions et le critère de vérification s’affichent.

**5ᵉ · Débutant**
- *Relier les appareils* : Brancher la salle · Le Wi-Fi de la salle
- *Les adresses IP* : Chacun son adresse · L’intrus · Le doublon
- *Les services du réseau* : Imprimer en réseau · Ma première page web

**4ᵉ · Intermédiaire**
- *Routeur et passerelle* : Relier deux salles · La passerelle oubliée
- *Observer les échanges* : Mais qui a cette adresse ? (ARP) · Le switch apprend
- *Diagnostiquer une panne* : Panne à trouver
- *Objets connectés* : Le capteur connecté

**3ᵉ · Avancé**
- *Sous-réseaux et masques* : Couper un réseau en deux (/25) · Le plan d’adressage du collège
- *Routage* : Deux routeurs, routes statiques · Le paquet qui tourne en rond (TTL)
- *Protocoles et encapsulation* : Ouvrir l’enveloppe

**Un seul panneau de guidage** : sans exercice, il affiche « Pour commencer » ; avec un exercice, il devient la Consigne. Chaque étape est expliquée en détail (« Comment faire » : où cliquer, quoi taper, ce qu’on doit voir) avec une petite capture animée de l’action : outil Câble puis les deux appareils, champ à remplir, icône du terminal et commande, barre d’adresse du navigateur, menu Réglages, tiroir des trames… L’aide diminue avec la difficulté : complète la première fois qu’une action apparaît et en 5ᵉ, repliée en simple rappel ensuite, puis réduite à « Besoin d’aide ? » en 3ᵉ. Quand un ping, une page ou une impression échoue, la cause probable s’affiche dans la consigne.

**Progression** : dans la liste des exercices, ceux qui ne sont pas encore faits sont grisés, ceux qui sont terminés sont cochés en vert, avec un compteur par classe. La progression est gardée dans le navigateur de l’élève.

Astuce pour l’enseignant : préparer un schéma, désactiver les alertes dans les Réglages pour un exercice de recherche de panne, puis l’exporter et le distribuer aux élèves (la consigne est incluse dans le fichier).

## Simplifications

Pas de DNS (on tape les adresses IP), pas de poignée de main TCP, pas de DHCP. Le smartphone et l’ESP32 sont reliés par une liaison Wi-Fi dessinée en pointillés.
