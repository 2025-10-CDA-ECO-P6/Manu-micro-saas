# 🏍️ MotoConnect

> **Trouve · Roule · Connecte** — L'app qui connecte les motards en temps réel.

---

## 📋 Présentation du projet

MotoConnect est une application web et mobile qui permet aux motards de se localiser mutuellement en temps réel sur une carte interactive, d'organiser des balades groupées et d'échanger au sein d'une communauté de riders.

Projet fil rouge — Licence Concepteur Développeur d'Application (CDA) 2024–2025.

---

## 🎯 Problème identifié

- Impossible de savoir si d'autres motards roulent près de toi en ce moment
- Organiser une balade de groupe = des dizaines de messages WhatsApp
- Aucune application dédiée à la communauté moto en France

---

## 👤 Public cible

| Profil | Description |
|---|---|
| Rider du weekend | Balades tranquilles, découverte de routes |
| Sportif | Cols, performance, routes sinueuses |
| Tourer | Longs trajets, road trips |
| Urbain | Mobilité quotidienne, scooter/moto |

**Marché** : 4 millions de motards en France.

---

## ✨ Fonctionnalités

### Fonctionnalité principale
- **Carte temps réel** — Voir les riders connectés autour de soi via géolocalisation live (WebSocket / Socket.io), avec filtres par style de ride (Sportif, Touring, Peinard, Col)

### Fonctionnalités secondaires
- Création et participation à des balades (date, heure, point de départ, nombre de places)
- Chat de groupe lié à chaque balade
- Profil rider (moto, style de ride, historique des balades)
- Fil d'actualité communautaire (posts, photos)
- Mode invisible (ne pas apparaître sur la carte)

---

## 🛠️ Stack technique

### Frontend
| Technologie | Usage |
|---|---|
| React + Vite | Interface web (SPA, compatible Leaflet.js) |
| React Native + Expo | Application mobile iOS / Android |
| Leaflet.js | Carte interactive (web) |
| MapLibre | Carte interactive (mobile) |
| Tailwind CSS | Styles |

### Backend
| Technologie | Usage |
|---|---|
| Node.js + Express | API REST |
| Socket.io | Géolocalisation et chat en temps réel |
| JWT + Refresh Token | Authentification |
| bcrypt | Hashage des mots de passe |

### Base de données
| Technologie | Usage |
|---|---|
| PostgreSQL | Base de données principale |
| Prisma | ORM — interface entre Node.js et PostgreSQL (migrations, requêtes, typage) |
| Cloudinary | Stockage des photos (public_id en BDD, transformations via URL) |

### Déploiement
 
**Stratégie progressive :**
 
| Phase | Service | Usage | Coût |
|---|---|---|---|
| Développement | Neon | PostgreSQL serverless | Gratuit |
| Développement | Render | Backend Node.js | Gratuit (avec mise en veille) |
| Développement | Vercel | Frontend React + Vite | Gratuit |
| Staging | Railway | Backend + PostgreSQL groupés | ~5$/mois |
| Production | Kubernetes + Docker | Orchestration complète | Variable |
| Mobile | Expo EAS | Build iOS + Android | Gratuit (limité) |
 
> **Objectif** : déploiement Kubernetes en fin de projet si le temps le permet — ingress, services et deployments séparés pour le backend Node.js, Socket.io et PostgreSQL.

---

## 🗄️ Modèle Conceptuel de Données (MCD)

5 entités principales :

```
UTILISATEUR ──(organise 1,1 / 0,N)──► BALADE
UTILISATEUR ──(émet 1,N / 1,1)──────► LOCALISATION
UTILISATEUR ──(participe 1,N)────────► PARTICIPATION ◄──(0,N contient)── BALADE
UTILISATEUR ──(écrit 1,N / 1,1)──────► MESSAGE ◄──(appartient 1,N / 1,1)── BALADE
```
---

## 📊 Diagramme de cas d'utilisation

Trois cas d'utilisation principaux :

1. **S'inscrire** — Créer un compte + profil moto + autoriser la géolocalisation
2. **Se connecter** — Authentification JWT avec gestion du reset mot de passe
3. **Carte interactive** — Voir les riders en temps réel, filtrer, contacter, mode invisible

---

## 🖼️ Wireframes

Trois écrans principaux maquettés :

| Écran | Description |
|---|---|
| Carte live | Map avec pins riders, bottom sheet, badge LIVE |
| Créer une balade | Formulaire + mini-carte + slots de places |
| Profil rider | Stats, moto, tags style, historique balades |

---

## 🎨 Charte graphique

| Élément | Valeur |
|---|---|
| Couleur principale | `#1C1C1C` — Bitume |
| Couleur accent | `#C0392B` — Moto Red |
| Couleur secondaire | `#F5A623` — Ambre |
| Typographie titres | Syne Bold |
| Typographie corps | DM Sans Regular |
| Style | Dark, sobre, inspiré Strava |

---

## 🗺️ Roadmap

A définir

---