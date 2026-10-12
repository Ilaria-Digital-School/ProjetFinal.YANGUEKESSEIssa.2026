# Documentation Bloc 1 — Frontend Budget_App

**Dossier :** `Budget_App/Frontend/`  
**Livrable :** choix de conception, approche responsive, accessibilité, tests effectués.  
**Hors livrable (non traité ici) :** commentaires dans le code source, démo en ligne.

Ce document décrit **uniquement** ce que le Bloc 1 contient vraiment : des maquettes HTML/CSS/JS, sans serveur. L’authentification réelle, MySQL et l’API arrivent aux Blocs 2 et 3.

---

## 1. Objectif du Bloc 1

Livrer l’interface mobile-first de Budget_App avant de brancher un backend :

- montrer le parcours utilisateur (connexion → accueil → opérations du quotidien) ;
- figer le design (couleurs, composants, navigation) réutilisé ensuite par Node (pages HTML) et Symfony (Twig) ;
- valider le responsive et une accessibilité de base.

**Limite assumée :** le login et l’inscription sont simulés en JavaScript (`preventDefault` + `alert` + redirection). Aucune donnée n’est persistée. `index.html` peut appeler `http://localhost:3000/api/home` si le Bloc 2 tourne ; sinon le solde affiché reste le placeholder `€3.466,9`.

---

## 2. Choix de conception

### 2.1 Cible et contraintes

| Choix | Justification |
|--------|----------------|
| **Mobile-first** | Usage type « wallet » : consultation du solde et actions rapides sur téléphone. |
| **HTML5 + CSS + JS vanilla** | Cahier du Bloc 1 : pas de React/Vue. Preuve de maîtrise du DOM, `fetch`, événements. |
| **Bootstrap 5.3.3** | Grille, offcanvas, modales, formulaires, dark mode (`data-bs-theme`) sans réécrire un design system. |
| **Bootstrap Icons 1.11** | Pictogrammes cohérents (accueil, cartes, transferts, factures). |
| **CSS custom (`style.css`)** | Identité visuelle (violet `#5e35b1` / bleu `#026cdf`) au-dessus de Bootstrap, pas un thème 100 % générique. |
| **~40 pages HTML** | Une page = un écran du parcours (plus simple à présenter qu’un SPA). |
| **Pas de bundler** | Fichiers ouverts tels quels (double-clic ou serveur statique). CDN Bootstrap. |

### 2.2 Architecture des fichiers

```
Frontend/
├── Pages/                 # 40 écrans HTML
│   ├── ui-pages.html      # Plan de toutes les maquettes
│   ├── login.html / inscription.html / resetpassword.html …
│   ├── index.html         # Tableau de bord
│   ├── card.html / addnewcard.html / transfert.html / topup.html …
│   └── setting.html / profile.html / help-support.html …
└── Public/Assets/
    ├── style.css          # Thème + media queries
    ├── scriptdarklight.js # Thème clair / sombre (localStorage)
    └── images/
```

Les pages du Bloc 2 (`Backend/pages/`) et les templates Twig du Bloc 3 reprennent cette structure d’écrans.

### 2.3 Parcours et organisation des écrans

Quatre pôles, alignés sur le cahier des charges :

1. **Compte** — connexion, inscription, mot de passe oublié, profil, paramètres, centre de sécurité.  
2. **Argent** — solde (accueil), transactions, historique bancaire, cartes, recharge.  
3. **Envoi** — contacts, transfert wallet, transfert banque, confirmation.  
4. **Services** — factures (mobile, internet, électricité, eau), films, market, rewards, QR.

`Pages/ui-pages.html` sert de **plan de site** pour la démo orale.

### 2.4 Composants UI récurrents

- **Header dégradé** (`.mobile-header`) : identité + solde.  
- **Offcanvas** : menu hamburger et notifications (composants Bootstrap).  
- **Cartes arrondies** (`.stats-section`, `.responsive-card`) : blocs d’action.  
- **Barre basse** : navigation type application mobile (`padding-bottom` sur `body` pour ne pas masquer le contenu).  
- **Breadcrumb** (`aria-label="breadcrumb"`) sur les pages secondaires.  
- **Formulaires** : champs `required`, type `email` / `password`, bascule afficher/masquer le mot de passe.

### 2.5 Thème clair / sombre

`scriptdarklight.js` lit `localStorage.theme` (sinon le `prefers-color-scheme` système), pose `data-bs-theme` sur `<html>`, et synchronise l’interrupteur de `setting.html`.

### 2.6 Ce qui n’est pas du « vrai » métier

| Écran | Comportement réel Bloc 1 |
|--------|---------------------------|
| `login.html` | Simulation : champs non vides → alerte succès → `index.html` |
| `inscription.html` | Validation locale (email, MDP ≥ 6, confirmation) puis redirection login |
| Boutons Facebook / Google / Apple | UI uniquement |
| `index.html` solde | Placeholder, ou `fetch` Bloc 2 si Node est lancé |
| `film.html` | Appel TMDB (clé API dans la page — à ne pas présenter comme prod) |
| `addnewcard.html` / `edit-profil.html` | `fetch` vers des URLs placeholder (PHP / API fictive) |

---

## 3. Approche de responsivité

Principe : **mobile d’abord**, puis élargissement par media queries. Le CSS ne part pas d’un desktop qu’on « casse » ensuite.

### 3.1 Base (tous les viewports)

- `<meta name="viewport" content="width=device-width, initial-scale=1.0">` sur **les 40 pages** (vérifié).  
- `body` : `font-size: 14px`, fond `#f8f9fa`, `padding-bottom: 70px` pour la nav basse.  
- Grille Bootstrap (`container`, `row`, `col-6`, `d-flex`, `offcanvas`).  
- Largeur fluide : `.container-custom { width: 100% }`.

### 3.2 Points de rupture (`Public/Assets/style.css`)

| Breakpoint | Comportement |
|------------|----------------|
| **≤ 576 px** | Offcanvas pleine largeur ; action-sheet iOS resserré. |
| **≥ 768 px** (tablette) | `font-size: 15px` ; conteneur max 720 px centré ; grille 2 colonnes (`.grid-item` 50 %). |
| **≥ 1024 px** (laptop) | `font-size: 16px` ; conteneur max 960 px ; grille 3 colonnes ; `.nav-mobile` masqué, `.nav-desktop` affiché. |
| **≥ 1440 px** | Conteneur max 1200 px. |

Les cartes, listes de services et formulaires s’empilent naturellement en colonne unique sur petit écran (pas de largeur fixe en pixels pour le contenu principal).

### 3.3 Composants sensibles au viewport

- Menu : offcanvas (doigt / souris), pas un mega-menu desktop.  
- Modales Bootstrap (`modal-top`) pour gérer cartes / transferts depuis l’accueil.  
- Images profil / logos : `object-fit: cover`, largeurs limitées (`width="50"` etc.).

### 3.4 Comment le vérifier à l’oral

1. Ouvrir `index.html`.  
2. Réduire la fenêtre sous ~400 px : header, solde, actions, offcanvas.  
3. Élargir au-delà de 1024 px : typo plus grande, grille plus large, nav desktop si présente.

Aucune PWA / application native : hors périmètre.

---

## 4. Mesures d’accessibilité

Objectif du cahier des charges : HTML sémantique, icônes compréhensibles, contraste utilisable. **Pas d’audit WCAG 2.2 AA formalisé, pas de rapport Lighthouse 90 %.** Ce qui suit est mesuré sur les fichiers.

### 4.1 Mesures en place

| Mesure | Preuve dans le code |
|--------|---------------------|
| Langue de page | `lang="fr"` sur 39/40 pages |
| Titre d’onglet | `<title>` distinct sur chaque page |
| Viewport | présent partout (zoom navigateur possible) |
| Labels de formulaires | `inscription.html` : `label for="nom|email|motdepasse|…"` |
| Contrôles Bootstrap | `aria-label` breadcrumb, `aria-labelledby` / `aria-hidden` sur modales, `role="tab"` notifications |
| Images | `alt` sur logos et photos de contacts (21 pages avec `<img alt>`) |
| Contraste header | texte blanc sur dégradé `#5e35b1` → `#4a3cc9` |
| Dark mode | `data-bs-theme` + couleurs Bootstrap |
| Focus clavier | composants Bootstrap (boutons, liens, offcanvas) |

### 4.2 Écarts constatés (honnêtes)

- `addnewrecipient.html` : `lang="en"` alors que le contenu est en français.  
- Labels **visuels** sans attribut `for` sur : `login.html`, `changepw.html`, `createnewpwd.html`, `resetpassword.html`, `addnewcard.html`, `topup.html`, `transfert.html`, `transferbybank.html` (labels flottants positionnés en CSS). Un lecteur d’écran relie moins bien le champ.  
- Boutons menu / cloche : icônes Bootstrap, pas toujours de texte visible (le `role="button"` et le contexte aident, un `aria-label` dédié serait plus propre).  
- Contraste des textes `.text-muted` / labels gris `#888` : acceptable en thème clair, plus fragile en sombre.  
- Pas de skip-link « Aller au contenu », pas de CSS `:focus-visible` custom, pas de tests NVDA/VoiceOver documentés.

Ces écarts sont une **suite possible**, pas un blocage du MVP maquettes.

---

## 5. Tests effectués

Pas de suite Jest/Cypress sur le Bloc 1. Les tests sont **manuels + inventaire automatique des HTML**.

### 5.1 Inventaire automatique (août 2026)

Script d’audit sur `Pages/*.html` :

| Contrôle | Résultat |
|----------|----------|
| Nombre de pages | 40 |
| `lang` présent | 40/40 |
| `lang="fr"` | 39/40 (`addnewrecipient.html` = `en`) |
| `viewport` | 40/40 |
| `<title>` | 40/40 |
| Au moins un attribut `aria-*` | 34/40 |
| Au moins une image avec `alt` | 21/40 (les autres n’ont pas d’`<img>` ou un décoratif) |

### 5.2 Parcours manuel

| # | Parcours | Attendu | Statut |
|---|----------|---------|--------|
| 1 | `ui-pages.html` → chaque lien listé | Navigation vers l’écran | OK (plan de démo) |
| 2 | `login.html` : champs vides | Alerte « remplissez les champs » | OK |
| 3 | `login.html` : email + MDP | Redirection `index.html` | OK (simulation) |
| 4 | `inscription.html` : MDP ≠ confirmation | Alerte | OK |
| 5 | `inscription.html` : MDP < 6 | Alerte | OK |
| 6 | `index.html` : menu offcanvas, cloche, actions | Ouverture Bootstrap | OK |
| 7 | `setting.html` : interrupteur thème | `data-bs-theme` + localStorage | OK |
| 8 | Resize 375 / 768 / 1280 px sur accueil, login, transfert | Pas de débordement majeur, empilement | OK visuel |
| 9 | `index.html` sans Node | Solde placeholder | OK |
| 10 | `index.html` avec Node `localhost:3000` | Solde / txns si cookie/API | Pont vers Bloc 2 (optionnel) |

### 5.3 Navigateurs visés

Chrome / Firefox / Edge récents (cahier des charges). Pas de recette Safari iOS documentée ici.

### 5.4 Hors tests Bloc 1

Auth serveur, isolation SQL, Luhn, solde calculé : **Bloc 2 / 3**.

---

## 6. Guide utilisateur des maquettes (fonctionnalités principales)

Ouvrir les fichiers dans `Pages/` (ou via `ui-pages.html`).

### Compte

1. **Inscription** (`inscription.html`) — nom, email, mot de passe, confirmation.  
2. **Connexion** (`login.html`) — email + mot de passe, lien mot de passe oublié.  
3. **Profil / paramètres** (`profile.html`, `setting.html`, `edit-profil.html`, `changepw.html`, `security-center.html`, `notification-setting.html`).

### Tableau de bord

`index.html` : solde, récompenses, destinataires récents, raccourcis (transfert, cartes, factures), dernières opérations, market.

### Cartes et recharge

`card.html` → `addnewcard.html` / `cardinfo.html` ; `topup.html` pour créditer (maquette).

### Transferts

`mycontact.html` → `transfert.html` → `transferconfirm.html` ; ou `transferbybank.html`.

### Factures et extras

`bill.html`, `mobile.html`, `internet.html`, `electricity.html`, `waterbill.html`, `film.html`, `rewards.html`, `myqr.html`, `help-support.html`.

**Rappel :** ces écrans illustrent le produit. Les écritures en base se font après connexion au backend (Bloc 2 ou 3).
