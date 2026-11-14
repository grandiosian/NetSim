# NetSim — notes pour Claude

Simulateur de réseau pour la technologie au collège, en **une seule page** : tout est dans `index.html` (CSS en tête,
puis HTML, puis le JavaScript), sans build ni dépendance. Détails : [README.md](README.md) ; contrat avec la Centrale :
[PASSERELLE.md](PASSERELLE.md). Vue d'ensemble de l'écosystème : [../CLAUDE.md](../CLAUDE.md).

## Règles
- Textes visibles en français, simples (élèves de collège). Style du fichier : règles CSS et JS compacts sur une ligne ; gardez-le.
- NetSim est **la référence de la barre du haut commune** (SPECIFICATION de la Centrale, §2.6) : une modification de sa barre se reporte dans TechnoStudio, Établi 3D et le portail.
- L'application marche sans la Centrale. Le module `centrale-client.js` vient du serveur de la Centrale : testez une fonction récente avant de l'appeler (`Centrale.menuEleve && …`) et gardez l'ancien comportement en repli.
- Ne renommez aucun identifiant d'activité ni clé de données (du travail élève y est rattaché).
- `centrale.json` et `ateliers.json` décrivent les 52 ateliers. Après toute modification d'un atelier (titre, résumé, étapes, catégorie, classe, niveau), mettez-les à jour avec ce que donne `outils/verifier-descripteur.html`, puis revérifiez avec la même page.

## Repères dans `index.html`
- **Barre du haut** `header.topbar` : grille `.tb-gauche` (logo, `.tb-hist` annuler / rétablir, widget `#widgetCentrale`) / atelier `#bResume` au centre / `.tb-droite` (thème, roue `#bSet` → `#mSet`).
  - `majBarreHaut()` ajoute `.compacte` (logo seul) quand l'atelier ne peut pas être pile au centre.
  - Widget de l'élève : `creerBouton()`, `majBouton()`, puis `Centrale.boutonPrecedent()` pour « ← » et `Centrale.menuEleve()` au clic quand l'élève est connecté (hors connexion : `basculerProfil()`).
- **Annuler / rétablir** : `HIST` / `REDO`, `snap()` avant chaque modification, `undo()`, `redo()`, `majHist()` (boutons grisés), `restaurer()`. Ils ne marchent qu'en mode conception (`mode==='design'`). Raccourcis : Ctrl/⌘+Z, Ctrl+Y ou Ctrl/⌘+Maj+Z.
- **Modèle** : état `S` (appareils, câbles, zones), `newDevice`, `connect`, `afterEdit()` (dessin, propriétés, sauvegarde).
- **Visite guidée** : tableau des étapes `{sel, t, p}` (rechercher `sel:'.tb-hist'`).

## Tester
- Il n'y a pas de tests automatiques. Servir le dossier (`python3 -m http.server`) et vérifier dans le navigateur : sans connexion, puis connecté à une Centrale locale (copie dans `centrale/public/miroir/netsim/`).
- Après toute modification d'atelier : `outils/verifier-descripteur.html` doit dire que tout correspond.

## Publier
Push sur `main` → GitHub Pages (`.github/workflows/pages.yml`). Le miroir alwaysdata ne se met à jour qu'au déploiement suivant de la Centrale (voir `../CLAUDE.md` §7).
