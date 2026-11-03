# 🏍️ Kruse
> **Trouve · Roule · Connecte** — L'app qui connecte les motards autour d'une passion commune.

---

## 📋 Présentation du projet

Kruse est une application web et mobile qui permet aux motards de trouver des compagnons de route au bon moment — maintenant, dans quelques heures, ou dans quelques jours — en publiant une sortie visible par les riders proches.

Projet fil rouge — Licence Concepteur Développeur d'Application (CDA) 2025/2026.

---

## 🎯 Problème identifié

- Les motards veulent rouler ensemble mais n'ont aucun moyen simple de trouver des compagnons de route
- Organiser une balade = des dizaines de messages WhatsApp et aucune gestion des inscriptions
- Aucune application dédiée à la communauté moto en France

---

## 👤 Public cible

| Profil | Description |
|---|---|
| Le rider spontané | Sort quand l'envie lui prend, cherche de la compagnie au dernier moment |
| L'organisateur | Prépare ses sorties à l'avance, veut gérer un groupe structuré |
| Le découvreur | Nouveau dans une région, cherche des locaux qui connaissent les bonnes routes |
| L'observateur | Consulte l'app passivement, veut être notifié sans s'exposer |

**Marché** : 4 millions de motards en France.

---

## ✨ Fonctionnalités

### Fonctionnalité principale — Le fil des sorties

Un fil de sorties organisé en trois horizons temporels :

| Horizon | Exemple | Durée de vie |
|---|---|---|
| **Maintenant** | "Je suis au parking Odysseum, quelqu'un veut rider ?" | Expire en 2h |
| **Aujourd'hui** | "Je pars cet aprem vers 14h direction les Cévennes" | Expire le soir |
| **Planifié** | "Balade Ventoux vendredi 14h — 6 places" | Jusqu'à la date |

### Fonctionnalités secondaires

- **Inscriptions structurées** — mode automatique ou manuel (à la BlaBlaCar), liste d'attente ordonnée
- **Chat par sortie** — fil de discussion activé dès confirmation, messages système automatiques
- **Itinéraire avec étapes** — point de RDV + waypoints optionnels, export vers Waze / Google Maps / Apple Maps en 1 tap
- **Profil rider** — moto, style de ride, historique des sorties, stats
- **Carte contextuelle** — sorties immédiates sur la map, mode invisible
- **Notifications push** — rappel la veille, nouvelle sortie dans le rayon, place libérée

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
| Socket.io | Temps réel (sorties live, chat) |
| JWT + Refresh Token | Authentification |
| bcrypt | Hashage des mots de passe |
| Cloudinary | Stockage des photos (public_id en BDD) |

### Base de données
| Technologie | Usage |
|---|---|
| PostgreSQL | Base de données principale |
| Prisma | ORM — migrations, requêtes typées, schéma |

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

> **Objectif** : déploiement Kubernetes en fin d'année si le temps le permet — ingress, services et deployments séparés pour Node.js, Socket.io et PostgreSQL.

---

## 🗄️ Modèle Conceptuel de Données (MCD)

6 entités principales :

```
UTILISATEUR ──(organise 1,1 / 0,N)──────► BALADE
UTILISATEUR ──(émet 1,N / 1,1)──────────► LOCALISATION (visible + statut_rider)
UTILISATEUR ──(participe 1,N / 0,N)──────► PARTICIPATION ◄──(contient 1,N / 0,N)── BALADE
UTILISATEUR ──(écrit 1,N / 1,1)──────────► MESSAGE ◄──(appartient 1,N / 1,1)────── BALADE
BALADE ──(compose 1,1 / 0,N)────────────► ETAPE
```

Champs clés :
- `BALADE.mode_inscription` — `automatique` ou `manuel`
- `BALADE.horizon` — `maintenant`, `aujourd_hui`, `planifie`
- `BALADE.statut` — `planifiee`, `imminente`, `en_cours`, `terminee`, `annulee`
- `PARTICIPATION.statut` — `confirme`, `en_attente`, `annule`, `present`
- `PARTICIPATION.ordre_attente` — position dans la liste d'attente
- `LOCALISATION.statut_rider` — `disponible`, `en_balade`, `propose_sortie`
- `ETAPE.ordre` — ordre des waypoints dans l'itinéraire

---

## 📊 Diagramme de cas d'utilisation

Deux acteurs : **Rider** et **Admin**

| Module | Cas d'utilisation |
|---|---|
| Authentification | S'inscrire, Se connecter, Réinitialiser mot de passe |
| Profil | Gérer son profil |
| Fil des sorties | Consulter le fil, Publier une sortie, Filtrer les sorties |
| Balades | Créer une balade, S'inscrire à une balade, Gérer les demandes, Ajouter un itinéraire, Exporter vers GPS |
| Chat | Envoyer un message, Recevoir notifications |
| Administration | Modérer le contenu, Gérer les utilisateurs (Admin uniquement) |

> L'Admin utilise le même cas "Se connecter" que le Rider — le rôle est différencié par le JWT (`role: admin`).

---

## 🖼️ Wireframes

Quatre écrans principaux — palette Asphalt & Carbon (v5) :

| Écran | Description |
|---|---|
| Fil des sorties | Écran d'accueil — sorties triées par horizon, bouton "Publier une sortie" |
| Détail balade | Infos organisateur, itinéraire, slots riders, export GPS |
| Chat de sortie | Fil de discussion lié à la balade, messages système automatiques |
| Gestion des demandes | Vue organisateur — accepter/refuser, liste d'attente ordonnée |

---

## 🎨 Charte graphique

| Élément | Valeur |
|---|---|
| Fond principal | `#141414` — Asphalt |
| Surface | `#1E1E1E` — Carbon |
| Accent | `#D93025` — Moto Red |
| Texte | `#F5F3F0` — Chalk |
| Texte secondaire | `#888580` — Muted |
| Typographie titres | Syne Bold |
| Typographie corps | DM Sans Regular |
| Style | Dark, sportif, inspiré Ducati × Strava |

---

## 📁 Structure du dépôt

```
```

---

## 🗺️ Roadmap

A définir

---

