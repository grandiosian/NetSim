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

## Exercices intégrés

Menu **Fichier → Exemples et exercices**. Chaque exercice s’ouvre au bon niveau, avec son schéma de départ et une consigne à cocher.

**5ᵉ** : Brancher la salle · Chacun son adresse · L’intrus · Imprimer en réseau · Ma première page web

**4ᵉ** : Relier deux salles · Mais qui a cette adresse ? (ARP) · Panne à trouver · Le capteur connecté · Le switch apprend

**3ᵉ** : Couper un réseau en deux (/25) · Deux routeurs, routes statiques · Ouvrir l’enveloppe (encapsulation) · Le plan d’adressage du collège · Le paquet qui tourne en rond (TTL)

Astuce pour l’enseignant : préparer un schéma, désactiver les alertes dans les Réglages pour un exercice de recherche de panne, puis l’exporter et le distribuer aux élèves (la consigne est incluse dans le fichier).

## Simplifications

Pas de DNS (on tape les adresses IP), pas de poignée de main TCP, pas de DHCP. Le smartphone et l’ESP32 sont reliés par une liaison Wi-Fi dessinée en pointillés.
