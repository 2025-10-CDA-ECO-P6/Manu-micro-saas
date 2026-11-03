# Kruse — Cahier des charges technique
**Licence Concepteur Développeur d'Application — 2025/2026**

---

## Langages

**JavaScript**
L'ensemble du projet est écrit en JavaScript — frontend mobile et backend. Ce choix permet d'utiliser le même langage des deux côtés, de partager des schémas de validation (Zod) entre le client et le serveur, et de bénéficier d'un écosystème npm unifié. TypeScript n'est pas utilisé — Prisma génère automatiquement les types des modèles de données, ce qui apporte les principaux bénéfices du typage sans la complexité supplémentaire.

**SQL**
Utilisé via Prisma ORM. Les requêtes complexes (agrégations, requêtes géospatiales pour le rayon de recherche) sont écrites en SQL natif via `$queryRaw` lorsque Prisma ne couvre pas le besoin.

---

## Frontend — Application mobile

L'application est exclusivement mobile. Ce choix est dicté par les usages réels des motards : consulter le fil des sorties avant de partir, partager sa position GPS pendant qu'on roule, recevoir des notifications push en temps réel. Ces usages ne sont pas compatibles avec un navigateur web — ils nécessitent une application native iOS.

**React Native 0.81 + Expo SDK 52**
React Native permet d'écrire des composants JavaScript qui se compilent en vues natives iOS. Expo est retenu comme couche d'abstraction pour simplifier l'accès aux APIs natives (GPS, notifications, caméra) sans gérer directement le code Swift ou Objective-C. Le workflow utilisé est le **prebuild (CNG — Continuous Native Generation)** : le dossier `/ios` est généré à la demande depuis `app.json` via `npx expo prebuild`, jamais édité manuellement.

**NativeWind 4**
Équivalent de Tailwind CSS pour React Native. Permet d'utiliser les classes utilitaires Tailwind directement dans les composants React Native via la prop `className`. Les tokens de la charte graphique Kruse (couleurs, typographies) sont définis dans `tailwind.config.js`.

**React Navigation 6**
Bibliothèque de navigation standard pour React Native. Remplace React Router qui n'existe pas en mobile. L'architecture de navigation de Kruse utilise :
- **Stack Navigator** — navigation entre écrans (détail d'une sortie, chat, profil)
- **Tab Navigator** — barre de navigation principale (Fil / Carte / Mes sorties / Profil)

**react-native-maps**
Bibliothèque de carte native pour React Native. Configurée avec `useGoogleMapsOnIos: false` pour utiliser **MapKit iOS** nativement — aucune clé API Google requise, rendu natif Apple, performances optimales sur iPhone. La POC a validé son fonctionnement avec prebuild sur iPhone 16 Pro.

**Expo Location**
Module Expo pour accéder au GPS de l'iPhone. Gère les permissions iOS (`NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysUsageDescription`), la précision de la position et les mises à jour en arrière-plan pendant une sortie.

**Expo Notifications**
Module Expo pour les notifications push natives iOS. Gère l'enregistrement du device token (APNs), la réception des notifications en foreground et background, et les deep links vers la ressource concernée (sortie, chat).

**Socket.io-client 4**
Client Socket.io pour la communication temps réel depuis l'app mobile. Gère le chat de sortie (rooms `sortie:{id}`) et la géolocalisation des riders en temps réel (rooms `carte:{zone}`).

**Axios 1**
Client HTTP pour les appels à l'API REST backend. Intercepteurs configurés pour le rafraîchissement automatique du JWT quand l'access token expire.

**AsyncStorage**
Équivalent de localStorage pour React Native. Stockage persistant local des préférences utilisateur (token, thème, dernière position connue). Les tokens JWT sensibles sont stockés via **Expo SecureStore** (chiffré dans le keychain iOS) plutôt qu'AsyncStorage.

**React Hook Form 7 + Zod 3**
Gestion des formulaires (créer une sortie, inscription, profil) avec validation partagée entre le mobile et le backend via des schémas Zod communs.

---

## Backend

Le backend est identique qu'il serve une application web ou mobile — seul le frontend change.

**Node.js 20 LTS + Express 5**
Serveur API REST. Architecture Routes → Middlewares → Controllers → Services. Express est préféré à NestJS pour sa simplicité et sa flexibilité, cohérente avec le niveau licence.

**Socket.io 4.7**
Communication temps réel : chat de sortie (rooms `sortie:{id}`), notifications push serveur→client (rooms `user:{id}`), géolocalisation en temps réel. Préféré aux WebSockets natifs pour sa gestion automatique des reconnexions, son support des rooms et ses fallbacks.

**Prisma 5**
ORM avec client TypeScript typé généré depuis `schema.prisma`. Choisi pour les migrations versionnées, la protection contre les injections SQL par défaut, et l'explorateur visuel Prisma Studio.

**JWT (jsonwebtoken 9) + bcrypt**
Authentification stateless. L'access token (15 min) est transmis dans le header `Authorization: Bearer`. Le refresh token (7 jours) est stocké côté serveur en base de données et côté client dans Expo SecureStore. Les mots de passe sont hashés avec bcrypt (coût 12).

**Nodemailer 6**
Envoi d'emails transactionnels (confirmation d'inscription, réinitialisation de mot de passe, notification d'annulation). SMTP Gmail en développement, Brevo en production.

**Helmet + express-rate-limit + CORS**
Sécurisation de l'API :
- Helmet configure les headers HTTP sécurisés
- express-rate-limit limite à 5 tentatives de connexion par 15 min par IP
- CORS configuré pour autoriser uniquement les origines connues

**Cloudinary 2**
Stockage des avatars riders. Seul le `public_id` est stocké en base — l'URL est construite via le SDK pour permettre des transformations sans stocker plusieurs versions.

---

## Base de données

**PostgreSQL 16**
Base de données relationnelle. Choisie pour les contraintes d'intégrité (clés étrangères, unicité), le support des types UUID et ENUM, et les extensions géospatiales (PostGIS envisagée pour les requêtes par rayon en production).

### Stockage des données

Deux bases de données aux rôles distincts :

- **PostgreSQL** — données persistantes (profils, sorties, participations, messages, étapes)
- **Redis (Upstash)** — données éphémères temps réel (positions GPS des riders, TTL 30 secondes)

Cette séparation évite de polluer PostgreSQL avec des milliers de mises à jour de position par heure, et permet à Redis de nettoyer automatiquement les positions obsolètes via le TTL.

### Schéma — 6 entités

| Entité | Rôle |
|---|---|
| UTILISATEUR | Données d'authentification et profil rider |
| SORTIE | La sortie avec horizon, mode d'inscription et statut |
| PARTICIPATION | Association UTILISATEUR ↔ SORTIE avec statut et ordre d'attente |
| MESSAGE | Messages du chat liés à une sortie |
| LOCALISATION | Position GPS d'un rider (mise à jour en temps réel) |
| ETAPE | Waypoints d'un itinéraire liés à une sortie |

---

## Outils et environnement de développement

**Docker + Docker Compose**
L'environnement backend local est conteneurisé. Un `docker-compose.yml` lance PostgreSQL et Adminer en une commande. Le serveur Node.js tourne hors Docker en développement pour simplifier le hot reload.

**Xcode 16**
Requis pour compiler et déployer l'app React Native sur iPhone physique. Utilisé uniquement pour le build iOS — le code est écrit dans VS Code. Le workflow : `npx expo prebuild` génère le dossier `/ios`, Xcode compile et déploie sur l'iPhone, Metro bundler gère le Fast Refresh pendant le développement.

**ESLint + Prettier**
Linting et formatage configurés en pre-commit hook via Husky.

**Jest 29 + Supertest**
Tests unitaires et d'intégration sur l'API backend.

**GitHub + Git Flow**
Stratégie de branches : `main` (production) → `develop` (intégration) → `feature/nom` (une branche par ticket).

---

## Déploiement

### Mobile — iOS

| Phase | Outil | Usage | Coût |
|---|---|---|---|
| Développement | Xcode + iPhone physique | Build local, Fast Refresh | Gratuit |
| Staging | Expo EAS Build | Build cloud + TestFlight | Gratuit (limité) |
| Production | App Store | Distribution publique | 99$/an (compte Apple Developer) |

> Note : en phase de développement et pour la présentation du projet CDA, le déploiement via Xcode sur iPhone physique est suffisant. Le compte Apple Developer payant n'est pas requis pour valider le projet.

### Backend

| Service | Usage | Coût |
|---|---|---|
| Neon | PostgreSQL serverless managé | Gratuit (dev) |
| Render | Hébergement backend Node.js | Gratuit (veille après 15min inactivité) |
| Cloudinary | Stockage et transformation d'images | Gratuit (25GB) |
| Upstash Redis | Géolocalisation riders temps réel (TTL 30s) | Gratuit (256MB, 500K cmds/mois) |
| Brevo | Emails transactionnels | Gratuit (300/jour) |
| GitHub Actions | CI/CD automatisé | Gratuit |

### Kubernetes (Minikube — staging local)

L'expérience Kubernetes acquise en entreprise est mise à profit pour le déploiement staging. Minikube tourne sur le MacBook Pro M1 et expose l'API backend sur le réseau Wi-Fi local, accessible depuis l'iPhone physique.

Architecture Kubernetes locale :
```
Deployment: Kruse-api     (Node.js + Socket.io)
Deployment: Kruse-postgres (PostgreSQL)
Service: NodePort               (expose l'API sur le réseau local)
Secret: Kruse-secrets     (JWT_SECRET, DATABASE_URL, CLOUDINARY_*)
ConfigMap: Kruse-config   (variables non sensibles)
```

L'iPhone accède à l'API via l'IP locale du Mac (`ipconfig getifaddr en0`) et le port exposé par le NodePort. Cette configuration est documentée dans le dossier de projet pour le jury BC03.

---

## Versions résumées

| Technologie | Version |
|---|---|
| React Native | 0.81.x |
| Expo SDK | 52.x |
| NativeWind | 4.x |
| React Navigation | 6.x |
| react-native-maps | 1.x |
| Node.js | 20.x LTS |
| Express | 5.x |
| Socket.io | 4.7.x |
| Prisma | 5.x |
| PostgreSQL | 16.x |
| Zod | 3.x |
| Redis (Upstash) | 7.x |
| Jest | 29.x |
| Docker | 24.x |
| Xcode | 16.x |

---

*Kruse — Cahier des charges technique v3.0 · Mobile iOS · Redis + Render · Licence CDA 2025/2026*
