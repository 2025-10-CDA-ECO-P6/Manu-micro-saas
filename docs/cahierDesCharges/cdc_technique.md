# MotoConnect — Cahier des charges technique
**Licence Concepteur Développeur d'Application — 2025/2026**

---

## Langages

**JavaScript / TypeScript**
L'ensemble du projet est écrit en JavaScript, avec une migration progressive vers TypeScript côté backend. Ce choix permet d'utiliser le même langage sur le frontend et le backend, de partager des types et des schémas de validation entre les deux, et de bénéficier d'un écosystème npm unifié.

**SQL**
Utilisé via Prisma ORM. Les requêtes complexes (agrégations, requêtes géospatiales pour le rayon de recherche) sont écrites en SQL natif via `$queryRaw` lorsque Prisma ne couvre pas le besoin.

**HTML / CSS**
Standards web via React et Tailwind CSS. Pas de CSS-in-JS pour éviter la surcharge à l'exécution.

---

## Frameworks et bibliothèques principales

**React 18 + Vite 5**
React est retenu pour le frontend en raison de son modèle de composants et de son écosystème mature. Vite remplace Create React App pour des temps de build significativement plus courts et un rechargement à chaud instantané en développement.

**Tailwind CSS 3.4**
Framework CSS utility-first choisi pour sa cohérence de design et sa capacité à construire rapidement une interface avec une charte graphique personnalisée définie dans `tailwind.config.js`.

**Node.js 20 LTS + Express 5**
Le backend est un serveur Express minimaliste. Express est préféré à NestJS ou Fastify pour sa simplicité et sa flexibilité. L'architecture suit le modèle Routes → Middlewares → Controllers → Services.

**Socket.io 4.7**
Gère la communication temps réel : chat de sortie (rooms `sortie:{id}`), notifications push (rooms `user:{id}`), et géolocalisation en temps réel. Préféré aux WebSockets natifs pour sa gestion automatique des reconnexions et son support des rooms.

**Prisma 5**
ORM qui génère un client TypeScript typé à partir du schéma. Choisi pour les migrations versionnées, la sécurité contre les injections SQL par défaut, et l'explorateur visuel Prisma Studio.

**Leaflet.js 1.9 + React-Leaflet 4**
Bibliothèque de carte open source pour la carte interactive et les mini-cartes d'itinéraire. Préférée à Google Maps pour l'absence de coût d'API et la compatibilité avec les tuiles OpenStreetMap.

**React Hook Form 7 + Zod 3**
React Hook Form gère les formulaires avec des performances optimales. Zod définit les schémas de validation partagés entre le frontend et le backend — un schéma unique, une seule source de vérité.

**JWT (jsonwebtoken 9) + bcrypt**
Authentification stateless via JWT. L'access token (15 min) est transmis dans le header Authorization. Le refresh token (7 jours) est stocké en httpOnly cookie, inaccessible depuis JavaScript. Les mots de passe sont hashés avec bcrypt (coût 12).

**Nodemailer 6**
Envoi d'emails transactionnels (confirmation d'inscription, réinitialisation de mot de passe, notification d'annulation). SMTP Gmail en développement, Brevo en production.

**Helmet + express-rate-limit + CORS**
- Helmet configure les headers HTTP sécurisés (CSP, X-Frame-Options, HSTS)
- express-rate-limit limite à 5 tentatives de connexion par 15 min par IP
- CORS restreint les origines autorisées à l'URL du frontend en production

**Cloudinary 2**
Stockage des avatars. Seul le `public_id` est stocké en base de données — l'URL est construite via le SDK pour permettre des transformations sans stocker plusieurs versions.

**Axios 1**
Client HTTP côté frontend avec intercepteurs pour le rafraîchissement automatique du JWT.

---

## Base de données

**PostgreSQL 16**
Base de données relationnelle choisie pour la richesse des données structurées de MotoConnect et les contraintes d'intégrité (clés étrangères, contraintes d'unicité). PostgreSQL est préféré à MySQL pour son support natif des types UUID, ENUM et des extensions géospatiales (PostGIS, envisagée pour les requêtes par rayon).

### Schéma — 6 entités

| Entité | Rôle |
|---|---|
| UTILISATEUR | Données d'authentification et profil rider |
| SORTIE | La sortie avec horizon, mode d'inscription et statut |
| PARTICIPATION | Association UTILISATEUR ↔ SORTIE avec statut et ordre d'attente |
| MESSAGE | Messages du chat liés à une sortie |
| LOCALISATION | Position géographique d'un rider (temps réel) |
| ETAPE | Waypoints d'un itinéraire liés à une sortie |

---

## Outils et environnement de développement

**Docker + Docker Compose**
L'environnement local est entièrement conteneurisé. Un `docker-compose.yml` lance PostgreSQL, le backend Node.js et Adminer en une seule commande.

**ESLint + Prettier**
Linting et formatage configurés en pre-commit hook via Husky.

**Jest 29 + Supertest**
Tests unitaires et d'intégration sur l'API backend.

**GitHub + Git Flow**
Stratégie de branches : `main` (production) → `develop` (intégration) → `feature/nom` (une branche par ticket).

---

## Services externes

| Service | Usage | Coût |
|---|---|---|
| Neon | PostgreSQL serverless managé | Gratuit (dev) |
| Railway | Hébergement backend Node.js | ~5$/mois |
| Vercel | Hébergement frontend React | Gratuit |
| Cloudinary | Stockage et transformation d'images | Gratuit (25GB) |
| Brevo | Emails transactionnels | Gratuit (300/jour) |
| GitHub Actions | CI/CD automatisé | Gratuit |

---

## CI/CD

Pipeline GitHub Actions déclenché sur chaque push :
1. Lint — ESLint + Prettier
2. Tests — Jest avec rapport de couverture
3. Build — image Docker backend
4. Deploy — Railway (develop) + Vercel (frontend)

---

## Versions résumées

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

*MotoConnect — Cahier des charges technique v1.0 · Licence CDA 2025/2026*
