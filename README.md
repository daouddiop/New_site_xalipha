# Xalipha — Backend & Interface Admin

Backend Node.js/Express avec base de données SQLite (fichier local, aucune installation de serveur de BDD nécessaire) pour :
- récupérer les soumissions du formulaire de contact du site,
- gérer ces messages depuis une interface admin,
- ajouter / modifier / supprimer les services affichés sur le site.

## 1. Prérequis

- Node.js 18 ou plus récent (vérifiez avec `node --version`)

## 2. Installation

```bash
cd xalipha-backend
npm install
cp .env.example .env
```

Ouvrez `.env` et modifiez si besoin :
- `JWT_SECRET` : une longue chaîne aléatoire (obligatoire à changer en production)
- `ADMIN_EMAIL` / `ADMIN_PASSWORD` : les identifiants du compte admin qui sera créé

## 3. Initialiser la base de données

```bash
npm run seed
```

Ce script crée le fichier `db/xalipha.db`, un compte administrateur (avec l'email/mot de passe définis dans `.env`) et 3 services de départ (Développement Web, Applications Mobiles, IA & Chatbots).

## 4. Lancer le serveur en local

```bash
npm run dev
```
(ou `npm start` sans rechargement automatique)

Le serveur démarre sur **http://localhost:3000**

- Site public : http://localhost:3000
- Interface admin : http://localhost:3000/admin
- API : http://localhost:3000/api/...

## 5. Tester

1. Ouvrez http://localhost:3000, remplissez le formulaire de contact en bas de page et envoyez-le.
2. Ouvrez http://localhost:3000/admin, connectez-vous avec les identifiants définis dans `.env`.
3. Dans l'onglet **Messages**, vous devez voir le message envoyé à l'étape 1.
4. Dans l'onglet **Services**, ajoutez un nouveau service : il apparaîtra automatiquement sur le site public (section "Des solutions complètes") après rechargement de la page.

## 6. Structure du projet

```
xalipha-backend/
├── server.js              # point d'entrée Express
├── db/
│   ├── database.js        # connexion SQLite + création des tables
│   ├── seed.js             # crée le compte admin + services de départ
│   └── xalipha.db          # fichier de base de données (créé après le seed)
├── middleware/
│   └── auth.js             # vérification du token JWT admin
├── routes/
│   ├── auth.js              # POST /api/auth/login
│   ├── messages.js          # POST /api/contact (public) + gestion admin des messages
│   └── services.js          # GET /api/services (public) + gestion admin des services
└── public/
    ├── index.html           # le site vitrine (formulaire + services connectés à l'API)
    └── admin/
        ├── index.html       # page de connexion admin
        └── dashboard.html   # tableau de bord (messages + services)
```

## 7. Points d'API principaux

| Méthode | Route                          | Accès   | Description                          |
|---------|--------------------------------|---------|--------------------------------------|
| POST    | /api/contact                   | public  | Enregistre un message de contact     |
| POST    | /api/auth/login                | public  | Connexion admin (retourne un token)  |
| GET     | /api/messages/admin/list        | admin   | Liste tous les messages              |
| PATCH   | /api/messages/admin/:id/lu      | admin   | Marque un message lu / non lu        |
| DELETE  | /api/messages/admin/:id         | admin   | Supprime un message                  |
| GET     | /api/services                  | public  | Liste les services actifs            |
| GET     | /api/services/admin/all         | admin   | Liste tous les services (actifs ou non) |
| POST    | /api/services                  | admin   | Crée un service                      |
| PUT     | /api/services/:id               | admin   | Modifie un service                   |
| DELETE  | /api/services/:id                | admin   | Supprime un service                  |

Les routes "admin" nécessitent un header `Authorization: Bearer <token>` obtenu via `/api/auth/login`.

## 8. Remarque sur le design

Le fichier HTML fourni fait référence à des classes CSS (`.hero`, `.service-card`, etc.) mais aucune feuille de style n'était jointe. Le site fonctionnera donc sans mise en forme tant que vous n'aurez pas rebranché votre fichier CSS d'origine dans le `<head>` de `public/index.html`. Toute la logique (formulaire + services dynamiques) est indépendante du CSS et fonctionnera normalement une fois le style rebranché.

## 9. Prochaines étapes possibles (non incluses)

- Envoi d'un email de notification à chaque nouveau message (ex: via Nodemailer)
- Pagination des messages si le volume devient important
- Rôles multiples pour l'administration
- Déploiement (Render, Railway, VPS...) — actuellement prévu pour un usage local uniquement
