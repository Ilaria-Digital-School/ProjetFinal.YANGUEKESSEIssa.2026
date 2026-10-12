# Mémo des sigles — présentation Budget_App

À garder sous les yeux à l’oral. Les deux docs techniques ont aussi ce mémo en bas de fichier.

---

## Ce n’est pas un sigle : `B->>S`

C’est la **syntaxe d’un schéma** (outil Mermaid). Ce n’est plus utilisé dans la doc simplifiée.

| Morceau | Sens |
|---------|------|
| **B** | Navigateur (Browser) |
| **S** | Serveur |
| **DB** | Base de données |
| **->>** | envoie un message (flèche) |
| **-->>** | répond |

Exemple : `B->>S: POST /login` = le navigateur envoie email + mot de passe au serveur.

---

## Vocabulaire fréquent

| Mot | En une phrase |
|-----|----------------|
| **API** | Le serveur envoie des données (JSON), pas seulement du HTML. |
| **JSON** | Format `{"email": "a@b.fr"}`. |
| **HTML / CSS / JS** | Structure / style / comportement dans le navigateur. |
| **HTTP** | GET = lire, POST = créer, PUT = modifier, DELETE = supprimer. |
| **CRUD** | Créer, Lire, Modifier, Supprimer. |
| **JWT** | Jeton de connexion du **Bloc 2** (Node), 7 jours. |
| **Session** | Connexion du **Bloc 3** (Symfony) via cookie. |
| **cookie httpOnly** | Le JavaScript de la page ne peut pas lire ce cookie. |
| **bcrypt / hasher** | On stocke une empreinte du mot de passe, pas le mot de passe. |
| **MySQL** | La base (tables). |
| **ORM / Doctrine** | On code des objets PHP, l’outil génère le SQL (Bloc 3). |
| **Twig** | Fichiers HTML Symfony avec `{{ }}`. |
| **MVC** | Vue + contrôleur + données. |
| **MCD** | Qui est lié à qui (1 user → plusieurs cartes). Détail : `Docs/RELATIONS_ENTITES.md`. |
| **MLD** | Les tables SQL + les FK (`user_id`, `category_id`). |
| **ON DELETE CASCADE** | Si le user part, ses cartes / txns / contacts partent avec. |
| **ON DELETE SET NULL** | Si la catégorie part, la txn reste, juste sans libellé. |
| **UML** | Famille de schémas (cas d’usage, classes, enchaînements). |
| **user_id** | « Cette ligne appartient à cet utilisateur ». |
| **income / expense** | Revenu / dépense. |
| **last4** | 4 derniers chiffres de carte. |
| **Luhn** | Vérifie qu’un numéro de carte est bien formé (Bloc 2 seulement). |
| **P2P** | Transfert d’une personne à une autre. |
| **OTP** | Code temporaire. Simulé pour les transferts ; code à 6 chiffres (écrit dans les logs) pour le mot de passe oublié. |
| **XSS** | Attaque par script dans la page. |
| **CSRF** | Faux formulaire envoyé depuis un autre site. |
| **401** | Pas connecté. **403** = connecté mais interdit. **409** = email déjà pris. |
| **PSP / Stripe** | Vrai prestataire de paiement — **pas branché** dans le projet. |
| **fetch** | Fonction du navigateur qui envoie une requête HTTP au serveur sans recharger la page. |
| **api.js** | Fichier du Bloc 2 qui regroupe tous les appels au serveur (`api.topup(50)`, `api.getCards()`…). |
| **sessionStorage** | Petite mémoire du navigateur, gardée entre les pages, effacée à la fermeture de l’onglet (paiement en 3 pages). |
| **.env** | Fichier des secrets (mot de passe MySQL, clé JWT), jamais envoyé sur Git. |
| **handlers** | gestionnaire de code ou de processus chargé de réagir automatiquement à un événement ou à un type d'action précis. |

---

## Les 3 blocs en 3 lignes

1. **Frontend** — maquettes HTML, login simulé.
2. **Node** — serveur à la main, JWT, MySQL ; les pages du Frontend y sont branchées via `api.js`.
3. **Symfony** — même métier, framework PHP.
