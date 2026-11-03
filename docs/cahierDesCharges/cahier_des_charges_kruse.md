# Kruse — Cahier des charges
**Licence Concepteur Développeur d'Application — 2025/2026**

---

## 1. Expression du besoin

### La problématique

En France, 4 millions de personnes possèdent un permis moto. Pourtant, rouler à plusieurs reste une organisation laborieuse : les motards qui souhaitent partager une sortie n'ont aucun outil dédié pour se trouver, se coordonner et communiquer en temps réel.

Le problème se manifeste de trois façons concrètes :

**Le rider spontané** veut partir maintenant et ne sait pas qui est disponible autour de lui. Il envoie des messages dans des groupes WhatsApp, attend des réponses qui n'arrivent pas, et finit par partir seul.

**L'organisateur** prépare une sortie planifiée mais gère les inscriptions sur WhatsApp, les itinéraires sur Google Maps, et les participants sur une note papier ou un Google Sheets. Il n'a aucun outil qui regroupe tout cela.

**Le découvreur** vient d'arriver dans une nouvelle ville avec sa moto. Il ne connaît ni les autres riders locaux, ni les bonnes routes. Les groupes Facebook de sa région sont peu actifs et mal organisés.

### Pourquoi les solutions existantes ne suffisent pas

Les réseaux sociaux généralistes (Facebook, Instagram, WhatsApp) ont une communauté moto active mais ne sont pas conçus pour l'organisation de sorties en groupe : pas de gestion des inscriptions, pas de notion de places disponibles, pas de communication centralisée liée à une sortie, pas de carte pour localiser les riders.

Les applications spécialisées récentes (WeRide, On Wheels) existent mais se positionnent sur un segment social et communautaire large, avec une complexité qui s'éloigne du besoin immédiat : trouver des riders disponibles et organiser une sortie en quelques minutes.

### La réponse apportée par Kruse

Kruse est une application web qui permet à un motard de **publier une sortie en moins de 2 minutes**, de la rendre visible aux riders proches, et de gérer les inscriptions et la communication du groupe dans un seul endroit.

La différence fondamentale : Kruse introduit la notion d'**horizon temporel**. Une sortie n'est pas simplement un événement — c'est un signal qui dit "je veux rouler maintenant", "cet après-midi" ou "vendredi prochain". Ce classement par urgence temporelle permet à chaque rider de trouver immédiatement ce qui correspond à sa disponibilité du moment.

---

## 2. Cahier des charges fonctionnel

### La fonctionnalité principale — Le fil des sorties

Le cœur de Kruse est le **fil des sorties** : un flux de sorties organisées en trois horizons temporels, triées par urgence, consultable par tous les riders connectés.

Un rider peut publier une sortie en renseignant :
- L'horizon (maintenant / aujourd'hui / planifié)
- Le lieu de départ
- Le style de ride (sportif, peinard, col, touring)
- Le nombre de places disponibles
- Le mode d'inscription (automatique ou validation manuelle)

La sortie apparaît immédiatement dans le fil des autres riders. C'est cette fonctionnalité qui répond directement à la problématique : rendre visible en temps réel l'intention de rouler, pour n'importe quel horizon temporel.

Toutes les autres fonctionnalités découlent de cette feature principale :

### Fonctionnalités secondaires

**Authentification et profil**
Un rider crée un compte avec ses informations : prénom, moto (marque, modèle, année), style de ride préféré, et une photo de profil. Ces informations sont visibles par les autres riders avant de s'inscrire à une sortie — c'est ce qui permet de juger la compatibilité d'un groupe.

**Inscription à une sortie**
Deux modes d'inscription coexistent. En mode automatique, la place est réservée immédiatement si des places sont disponibles. En mode manuel, l'organisateur reçoit une demande et choisit d'accepter ou de refuser — une liste d'attente ordonnée gère les cas où toutes les places sont prises.

**Gestion des demandes (vue organisateur)**
L'organisateur voit le profil de chaque demandeur (moto, style, historique des sorties) avant de valider. Il gère sa liste de participants et sa liste d'attente depuis une interface dédiée.

**Itinéraire et export GPS**
Une sortie peut inclure des étapes (waypoints) affichées sur une mini-carte Leaflet. L'itinéraire est exportable en un tap vers Waze, Google Maps ou Apple Maps.

**Chat de sortie**
Chaque sortie dispose d'un fil de discussion accessible uniquement aux participants confirmés. Des messages système automatiques informent le groupe des événements clés (nouveau participant, annulation de sortie).

**Carte interactive**
Une carte Leaflet affiche les sorties actives géolocalisées. En complément, la position des riders est partagée en temps réel via WebSocket pour les riders qui activent cette option. Un mode invisible permet de ne pas apparaître sur la carte.

**Notifications**
Un système de notifications en application informe les riders des événements les concernant : inscription acceptée, sortie annulée, place libérée en liste d'attente. Des emails sont envoyés sur les événements critiques.

**Administration**
Un rôle administrateur permet de modérer le contenu et de gérer les utilisateurs. L'admin se connecte via la même interface que les riders — son rôle est différencié par le JWT.

### Hors scope

Les éléments suivants ne font pas partie du MVP :
- Application mobile native (web uniquement)
- Système de notation ou d'avis sur les riders
- Paiement ou abonnement
- Intégration réseaux sociaux (login Google/Facebook)
- Fil d'actualité communautaire (posts, photos)
- Calcul automatique d'itinéraire (Directions API)
- Détection de chute

---

## 3. Cahier des charges technique

### Langages

**JavaScript / TypeScript**
L'ensemble du projet est écrit en JavaScript, avec une migration progressive vers TypeScript côté backend. Ce choix permet d'utiliser le même langage sur le frontend et le backend, de partager des types et des schémas de validation entre les deux, et de bénéficier d'un écosystème npm unifié. TypeScript est introduit côté serveur pour renforcer la fiabilité des modèles de données Prisma.

**SQL**
Utilisé via Prisma ORM. Les migrations et les requêtes complexes (agrégations pour les statistiques de sorties, requêtes géospatiales pour le rayon de recherche) sont écrites en SQL natif via `$queryRaw` lorsque Prisma ne couvre pas le besoin.

**HTML / CSS**
Standards web via React et Tailwind CSS. Pas de CSS-in-JS pour éviter la surcharge à l'exécution.

### Frameworks et bibliothèques principales

**React 18 + Vite**
React est retenu pour le frontend en raison de son modèle de composants et de son écosystème mature (React Router, React Hook Form, React Query). Vite remplace Create React App pour des temps de build significativement plus courts et un rechargement à chaud instantané en développement.
Version : React 18.3 / Vite 5.x

**Tailwind CSS 3**
Framework CSS utility-first choisi pour sa cohérence de design et sa capacité à construire rapidement une interface avec une charte graphique personnalisée (tokens CSS définis dans `tailwind.config.js`). Il évite la fragmentation des fichiers CSS et simplifie la maintenance.
Version : Tailwind CSS 3.4

**Node.js + Express 5**
Le backend est un serveur Express minimaliste. Express est préféré à NestJS ou Fastify pour sa simplicité d'apprentissage et sa flexibilité, cohérente avec le niveau licence. L'architecture suit le modèle Routes → Middlewares → Controllers → Services.
Version : Node.js 20 LTS / Express 5.x

**Socket.io 4**
Gère la communication temps réel : chat de sortie (rooms par sortie identifiées `sortie:{id}`), notifications push (rooms personnelles `user:{id}`), et géolocalisation en temps réel. Socket.io est préféré aux WebSockets natifs pour sa gestion automatique des reconnexions, son support des rooms et ses fallbacks (long-polling si WebSocket indisponible).
Version : Socket.io 4.7

**Prisma 5**
ORM qui génère un client TypeScript typé à partir du schéma `schema.prisma`. Choisi pour trois raisons : les migrations versionnées (`prisma migrate dev`), la sécurité contre les injections SQL par défaut, et l'explorateur visuel (`prisma studio`) utile pendant le développement.
Version : Prisma 5.x

**Leaflet.js 1.9**
Bibliothèque de carte open source utilisée pour la carte interactive et les mini-cartes d'itinéraire. Préférée à Google Maps pour l'absence de coût d'API et la compatibilité avec les tuiles OpenStreetMap.
Version : Leaflet 1.9 + React-Leaflet 4.x

**React Hook Form + Zod**
React Hook Form gère les formulaires (créer une sortie, inscription) avec des performances optimales (pas de re-render à chaque frappe). Zod définit les schémas de validation partagés entre le frontend (validation côté client) et le backend (validation côté serveur). Un schéma unique = une seule source de vérité.
Versions : RHF 7.x / Zod 3.x

**JSON Web Token (jsonwebtoken) + bcrypt**
Authentification stateless via JWT. L'access token (expiration 15 minutes) est transmis dans le header Authorization. Le refresh token (expiration 7 jours) est stocké en httpOnly cookie pour être inaccessible depuis JavaScript, réduisant le risque de vol par XSS. Les mots de passe sont hashés avec bcrypt (coût 12).
Versions : jsonwebtoken 9.x / bcryptjs 2.x

**Nodemailer**
Envoi d'emails transactionnels (confirmation d'inscription, réinitialisation de mot de passe, notification d'annulation). Configuré avec un compte SMTP (Gmail en développement, service dédié type Brevo en production).
Version : Nodemailer 6.x

**Helmet + express-rate-limit + CORS**
Sécurisation de l'API Express :
- Helmet configure automatiquement les headers HTTP sécurisés (Content-Security-Policy, X-Frame-Options, HSTS)
- express-rate-limit limite à 5 tentatives de connexion par 15 minutes par IP pour contrer le brute force
- CORS restreint les origines autorisées à l'URL du frontend en production
Versions : Helmet 7.x / express-rate-limit 7.x

**Cloudinary SDK**
Stockage des photos de profil (avatars). Le fichier est uploadé depuis le frontend via Multer (middleware Express), transmis à Cloudinary, et seul le `public_id` est stocké en base de données. L'URL de l'image est construite à la demande via le SDK Cloudinary, permettant des transformations (redimensionnement, recadrage) sans stocker plusieurs versions.
Version : cloudinary 2.x / multer 1.x

**Axios**
Client HTTP côté frontend avec intercepteurs configurés pour le rafraîchissement automatique du token JWT (si l'access token expire pendant une requête, l'intercepteur appelle silencieusement /auth/refresh avant de rejouer la requête).
Version : Axios 1.x

### Base de données

**PostgreSQL 16**
Base de données relationnelle choisie pour la richesse des données structurées de Kruse (relations entre utilisateurs, sorties, participations, messages) et pour les contraintes d'intégrité (clés étrangères, contraintes d'unicité). PostgreSQL est préféré à MySQL pour son support natif des types `UUID`, `ENUM` et des extensions géospatiales (PostGIS, envisagée pour les requêtes par rayon en production).

**Schéma — 6 entités principales :**
- `UTILISATEUR` — données d'authentification et profil rider
- `SORTIE` — la sortie avec son horizon, mode d'inscription et statut
- `PARTICIPATION` — association UTILISATEUR ↔ SORTIE avec statut et ordre d'attente
- `MESSAGE` — messages du chat liés à une sortie
- `LOCALISATION` — position géographique d'un rider (mise à jour en temps réel)
- `ETAPE` — waypoints d'un itinéraire liés à une sortie

### Outils et environnement de développement

**Docker + Docker Compose**
L'environnement local est entièrement conteneurisé. Un `docker-compose.yml` lance PostgreSQL, le backend Node.js et Adminer (interface web BDD) en une seule commande. Garantit la reproductibilité entre machines.

**ESLint + Prettier**
Linting (ESLint avec la config Airbnb) et formatage automatique (Prettier) configurés en pre-commit hook via Husky. Maintient la cohérence du code sur la durée du projet.

**Jest + Supertest**
Tests unitaires et d'intégration sur l'API backend. Jest est le runner de tests standard de l'écosystème Node.js. Supertest permet de tester les routes Express sans démarrer un serveur HTTP réel.

**GitHub + Git Flow**
Versionnement du code avec une stratégie de branches :
- `main` — code de production, protégé (pas de push direct)
- `develop` — branche d'intégration
- `feature/nom-feature` — une branche par ticket

**Notion**
Gestion de projet et documentation technique (personas, MCD, UML, wireframes, tickets de développement, dossier CDA).

### Services externes

| Service | Usage | Coût |
|---|---|---|
| **Neon** | PostgreSQL serverless managé | Gratuit (développement) |
| **Railway** | Hébergement backend Node.js | ~5$/mois (staging/production) |
| **Vercel** | Hébergement frontend React | Gratuit |
| **Cloudinary** | Stockage et transformation d'images | Gratuit (25GB) |
| **Brevo** (ex-Sendinblue) | Envoi d'emails transactionnels | Gratuit (300 emails/jour) |
| **GitHub Actions** | CI/CD automatisé | Gratuit |

### CI/CD

Pipeline GitHub Actions déclenché sur chaque push :
1. **Lint** — ESLint + Prettier
2. **Tests** — Jest avec rapport de couverture
3. **Build** — Build Docker de l'image backend
4. **Deploy** — Déploiement automatique sur Railway (branche `develop`) et Vercel (frontend)

### Versions résumées

| Technologie | Version |
|---|---|
| Node.js | 20.x LTS |
| React | 18.3.x |
| Vite | 5.x |
| Express | 5.x |
| Socket.io | 4.7.x |
| Prisma | 5.x |
| PostgreSQL | 16.x |
| Tailwind CSS | 3.4.x |
| Leaflet.js | 1.9.x |
| Zod | 3.x |
| Jest | 29.x |
| Docker | 24.x |

---

*Kruse — Cahier des charges v1.0 · Licence CDA 2025/2026*
