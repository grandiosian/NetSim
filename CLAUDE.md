# NetSim — notes pour Claude

Simulateur de réseau pour la technologie au collège, en **une seule page** : tout est dans `index.html` (CSS en tête,
puis HTML, puis le JavaScript), sans build ni dépendance. Détails : [README.md](README.md) ; contrat avec la Centrale :
[PASSERELLE.md](PASSERELLE.md). Vue d'ensemble de l'écosystème : [../CLAUDE.md](../CLAUDE.md).

## Règles
- Textes visibles en français, simples (élèves de collège). Style du fichier : règles CSS et JS compacts sur une ligne ; gardez-le.
- **Charte graphique** : [CHARTE-GRAPHIQUE.md](CHARTE-GRAPHIQUE.md) (logo, couleurs clair / sombre, polices) est à respecter. Un changement de logo ou de couleur principale se fait d'abord dans ce document, puis dans le code et dans le portail de la Centrale (`centrale/public/index.php` : `LOGOS` et `.app-…`).
- NetSim est **la référence de la barre du haut commune** (SPECIFICATION de la Centrale, §2.6) : une modification de sa barre se reporte dans TechnoStudio, Établi 3D et le portail.
- L'application marche sans la Centrale. Le module `centrale-client.js` vient du serveur de la Centrale : testez une fonction récente avant de l'appeler (`Centrale.menuEleve && …`) et gardez l'ancien comportement en repli.
- Ne renommez aucun identifiant d'activité ni clé de données (du travail élève y est rattaché).
- `centrale.json` et `ateliers.json` décrivent les 52 ateliers. Après toute modification d'un atelier (titre, résumé, étapes, catégorie, classe, niveau), mettez-les à jour avec ce que donne `outils/verifier-descripteur.html`, puis revérifiez avec la même page.

## Repères dans `index.html`
- **Barre du haut** `header.topbar` : grille `.tb-gauche` (logo, `.tb-hist` annuler / rétablir, widget `#widgetCentrale`) / atelier `#bResume` au centre / `.tb-droite` (thème, roue `#bSet` → `#mSet`).
  - `majBarreHaut()` ajoute `.compacte` (logo seul) quand l'atelier ne peut pas être pile au centre.
  - Widget de l'élève : `creerBouton()`, `majBouton()`, puis `Centrale.boutonPrecedent()` pour « ← » et `Centrale.menuEleve()` au clic quand l'élève est connecté (hors connexion : `basculerProfil()`).
  - Le widget affiche `Centrale.nomAffiche()` (vue Enseignant d'un essai : nom gardé sur l'ordinateur de l'enseignant, ou « pas désanonymisé » en italique) et se redessine avec `Centrale.surChangementVue`.
- **Annuler / rétablir** : `HIST` / `REDO`, `snap()` avant chaque modification, `undo()`, `redo()`, `majHist()` (boutons grisés), `restaurer()`. Ils ne marchent qu'en mode conception (`mode==='design'`). Raccourcis : Ctrl/⌘+Z, Ctrl+Y ou Ctrl/⌘+Maj+Z.
- **Modèle** : état `S` (appareils, câbles, zones), `newDevice`, `connect`, `afterEdit()` (dessin, propriétés, sauvegarde).
- **Mode atelier** (ouvert depuis la Centrale) : `MODE_AT`, lu dans l'adresse avant le module de la Centrale et gardé dans `sessionStorage` (`netsim-mode`) ; classe `ns-atelier` sur `<html>`. `ATELIERS_NS` déclare les ateliers composés (`premiers-pas`) et la session libre (`#libre`), publiés en tête de `NetSim.ateliers()`. `entrerAtelier()` choisit l'exercice (au démarrage et après la connexion), `prochainExo()` donne le suivant, `openParc()` ne fait rien. Une étape `ha:1` est validée d'office en mode atelier ; `ta` remplace son texte dans un atelier composé. Règles de l'atelier (SPECIFICATION §2.6) : `regleAtelier('menu'|'bibliotheque'|'niveau')`, fermé par défaut (`INTERFACE_ATELIER`), rouvert par un champ `interface:{…}` d'un atelier (`ATELIERS_NS`) ou d'un exercice (`EXOS`), publié dans `ateliers.json` ; classes `ns-sans-…` de `<html>`.
- **Essai de l'enseignant** (« Tester à la place d'un élève ») : `Centrale.boutonVue()` posé en tête de `.tb-droite` (collé à l'atelier), dans `majBouton()`. Vue enseignant (`vueProfNS()`, classe `ns-vue-prof`) : `regleAtelier()` permet tout et les compétences visées s'affichent.
- **Navigation de la Centrale** (« ← » / « Suivant → ») : pied `#exoNav` du panneau consigne, dessiné par `majNavConsigne(nav)` (état `NAV_C`, null hors de la Centrale) ; `Centrale.navigation(…, {activite})` est branché dans `demarrer()` (module récent seulement, sinon les boutons flottants du module restent). Quand la Centrale navigue, la fin d'exercice n'affiche plus ses propres boutons « Exercice suivant » / « Retour à mes ateliers ». `centrale_atelier=<atelier composé>` dans l'adresse garde l'atelier composé (`MODE_AT`) après « Suivant » (page rechargée sans `#atelier=`).
- **Visite guidée** : tableau des étapes `{sel, t, p}` (rechercher `sel:'.tb-hist'`).

## Tester
- Il n'y a pas de tests automatiques. Servir le dossier (`python3 -m http.server`) et vérifier dans le navigateur : sans connexion, puis connecté à une Centrale locale (copie dans `centrale/public/miroir/netsim/`).
- Après toute modification d'atelier : `outils/verifier-descripteur.html` doit dire que tout correspond.

## Publier
Push sur `main` → GitHub Pages (`.github/workflows/pages.yml`). Le miroir alwaysdata ne se met à jour qu'au déploiement suivant de la Centrale (voir `../CLAUDE.md` §7).
