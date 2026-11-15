# Charte graphique — NetSim

Ce document fixe l'identité visuelle de NetSim. Il s'impose à l'application elle-même, au portail de la Centrale (cartes d'atelier, pastille du haut), aux fiches et aux documents qui présentent NetSim.

Pour changer un élément, on modifie d'abord ce document, puis :
- le code de NetSim (`index.html`) ;
- le portail de la Centrale (`centrale/public/index.php` : `LOGOS.netsim` et la classe `.app-netsim`).

Règles communes aux applications élèves (barre du haut, mesures) : [SPECIFICATION de la Centrale, §2.6](../centrale/public/passerelle/SPECIFICATION.md). NetSim y sert de référence.

## 1. Identité

| | |
|---|---|
| Nom | **NetSim**, écrit « Net**Sim** » dans la barre (« Sim » en gras) |
| Identifiant | `netsim` (ne change jamais) |
| Rôle | Simulateur de réseau : câblage, adresses IP, routeurs, trames animées |
| Icône de secours | 🌐 (`centrale.json`, champ `icone`), seulement là où le logo ne peut pas s'afficher |

## 2. Logo

Trois appareils reliés : un triangle de liaisons et trois nœuds. Le nœud du haut est à la couleur principale, ceux du bas reprennent les couleurs de deux protocoles (HTTP en vert, ARP en orange).

```svg
<svg viewBox="0 0 28 28">
  <path d="M7 20L14 7l7 13z" fill="none" stroke="#4a5b82" stroke-width="2"/>
  <circle cx="14" cy="7" r="4" fill="#1c3268"/>
  <circle cx="7" cy="20" r="4" fill="#178a4c"/>
  <circle cx="21" cy="20" r="4" fill="#d98a00"/>
</svg>
```

- **Où il se trouve** : `header.topbar .brand` et l'icône de l'onglet (`<link rel="icon">`) de `index.html`.
- **Thème sombre** : le trait passe en `#6c7a99`, les nœuds en `#9db4f2`, `#4cc584` et `#f0b03a`. Dans l'application, ce sont les variables `--ink-3`, `--accent`, `--http` et `--arp`.
- **Tailles** :
  - barre du haut : 26 px ;
  - tuile du portail : 64 % de la tuile, sur un fond `--app` à 13 %.
- **Interdits** :
  - changer la couleur ou l'ordre des nœuds ;
  - remplir le triangle ;
  - ajouter une ombre ;
  - déformer le logo ;
  - le poser sur une photo.

## 3. Couleurs

| Rôle | Clair | Sombre | Variable |
|---|---|---|---|
| **Couleur principale** (portail : `--app`) | `#1c3268` bleu marine | `#9db4f2` | `--accent`, `--tile` |
| **Bande des cartes du portail** (bleu marine) | `#1c3268` | `#3f62b8` | portail : `--app-bande` |
| Fond | `#f4f6fb` | `#0f1729` | `--paper` |
| Surface (panneaux) | `#ffffff` | `#151f35` | `--surface` |
| Texte | `#14213d` | `#e6ecf8` | `--ink` |
| Texte secondaire | `#4a5b82` | `#6c7a99` | `--ink-3` |
| Traits | `#d5dbe8` | `#2a3858` | `--line` |
| Réussite, HTTP | `#178a4c` | `#4cc584` | `--ok`, `--http` |
| Consigne, alerte, ARP | `#d98a00` | `#f0b03a` | `--warn`, `--arp` |
| Erreur | `#d23b3b` | `#ff6f6f` | `--err` |
| Trames | ICMP `#2c7be5`, DHCP `#7a4fd6`, DNS `#0e8a8a` | ICMP `#5aa2ff`, DHCP `#b59cff`, DNS `#4fd1d1` | `--icmp`, `--dhcp`, `--dns` |

- **Jaune de consigne** (`--warn`) : le panneau Consigne porte un liseré gauche de 4 px et un titre en petites capitales de cette couleur, comme le Guide d'Établi 3D.
- **Couleurs de protocole** : elles ont un sens (une trame ARP est toujours orange). Ne pas les utiliser pour décorer.

## 4. Typographies

| Usage | Police | Variable |
|---|---|---|
| Titres, logo | Bricolage Grotesque (600, 800) | `--f-disp` |
| Texte | Atkinson Hyperlegible (400, 700) | `--f-body` |
| Adresses, commandes | JetBrains Mono | `--f-mono` |

## 5. Règles d'usage

- **Barre du haut** : la référence commune (52 px, pilules de 34 px, boutons 36 × 34).
- **Panneaux** : verre dépoli (`.glass`), coins de 18 px.
- **Écran de travail** : grille isométrique sur fond `--paper`.
- **Textes visibles** : en français, simples, pour des élèves de collège.
