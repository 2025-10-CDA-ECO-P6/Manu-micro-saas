# 🎫 Template de ticket — MotoConnect (Notion)

> Crée une base de données Notion avec ces propriétés.
> Chaque ticket = une carte dans cette base.

---

## 📋 Propriétés de la base de données

| Propriété | Type Notion | Valeurs possibles |
|---|---|---|
| **Titre** | Title | Nom du ticket |
| **ID** | Text | MC-001, MC-002... |
| **Statut** | Select | Backlog / Conception / À faire / En cours / Review / Test / Terminé |
| **Phase** | Select | Phase 0 / Phase 1 / Phase 2 / Phase 3 / Phase 4 / Phase 5 / Phase 6 |
| **Bloc CDA** | Multi-select | BC01 / BC02 / BC03 |
| **Catégorie** | Select | Auth / Profil / Sortie / Inscription / Chat / Carte / Notif / Tests / DevOps / Conception / Dossier |
| **Priorité** | Select | 🔴 Indispensable / 🟡 Important / 🟢 Bonus |
| **Effort** | Select | XS (1h) / S (2-3h) / M (4-6h) / L (1-2j) / XL (3j+) |
| **Couche** | Multi-select | Frontend / Backend / BDD / DevOps |
| **Sprint** | Number | 1, 2, 3... |
| **Lié à** | Relation | Lien vers d'autres tickets |
| **Doc associée** | URL | Lien vers la page Notion de doc |
| **Date de début** | Date | — |
| **Date de fin** | Date | — |
| **Notes** | Text | Remarques courtes |

---

## 📄 Structure d'un ticket (corps de page)

```markdown
## 📌 Description
[Décris ce que ce ticket doit accomplir en 2-3 phrases. 
Contexte métier + objectif technique.]

---

## ✅ Critères d'acceptation
- [ ] Critère 1 — ce qui doit fonctionner pour que le ticket soit "done"
- [ ] Critère 2
- [ ] Critère 3
- [ ] Tests passent (si applicable)
- [ ] Code reviewé (même en solo — relecture à tête reposée)
- [ ] Documenté si nécessaire

---

## 🔗 Dépendances
- Nécessite : [ID ticket dont ce ticket dépend]
- Bloque : [ID ticket que ce ticket débloque]

---

## 🛠️ Notes techniques
[Architecture, endpoints API, composants React, schéma BDD concerné...]

---

## 🧪 Scénarios de test
| Scénario | Entrée | Résultat attendu |
|---|---|---|
| Cas nominal | ... | ... |
| Cas limite | ... | ... |
| Cas erreur | ... | ... |

---

## 📎 Ressources
- [Lien doc technique, RFC, OWASP, doc Prisma, etc.]

---

## 📝 Journal
- [Date] — [Ce qui a été fait / décision prise]
```

---

## 🎫 Tous les tickets pré-remplis

---

### MC-001 — Setup environnement Docker dev
**Phase :** 1 | **Bloc :** BC03 | **Priorité :** 🔴 | **Effort :** M | **Couche :** DevOps

**Description :**
Mettre en place l'environnement de développement local avec Docker Compose. PostgreSQL + Node.js + Adminer accessibles en une seule commande (`docker-compose up`).

**Critères d'acceptation :**
- [ ] `docker-compose.yml` fonctionnel avec services postgres, api, adminer
- [ ] Variables d'environnement dans `.env` (jamais commité)
- [ ] `.env.example` commité avec les clés sans valeurs
- [ ] `README.md` mis à jour avec les instructions d'installation
- [ ] Adminer accessible sur localhost:8080

---

### MC-002 — Initialiser le repo GitHub
**Phase :** 1 | **Bloc :** BC03 | **Priorité :** 🔴 | **Effort :** S | **Couche :** DevOps

**Description :**
Créer et structurer le repo GitHub avec les dossiers client, server et docs. Configurer le gitignore, les branches et la protection de la branche main.

**Critères d'acceptation :**
- [ ] Repo créé (public ou privé selon choix)
- [ ] Structure : `/client`, `/server`, `/docs` (MCD, UML, wireframes)
- [ ] `.gitignore` complet (node_modules, .env, dist)
- [ ] Branche `main` protégée — pas de push direct
- [ ] Branches : `main` (prod), `develop` (intégration), `feature/*` (fonctionnalités)
- [ ] README à la racine

---

### MC-003 — Initialiser Prisma + schéma BDD
**Phase :** 1 | **Bloc :** BC02 | **Priorité :** 🔴 | **Effort :** M | **Couche :** BDD

**Description :**
Initialiser Prisma ORM et modéliser les 6 entités du MCD : UTILISATEUR, SORTIE, PARTICIPATION, MESSAGE, LOCALISATION, ETAPE. Première migration et seeds de données de test.

**Critères d'acceptation :**
- [ ] `prisma/schema.prisma` avec les 6 modèles complets
- [ ] Première migration appliquée (`prisma migrate dev`)
- [ ] Seeds créant 3 utilisateurs, 2 sorties, quelques participations
- [ ] Prisma Studio fonctionnel (`prisma studio`)
- [ ] Relations correctes entre les modèles

**Notes techniques :**
```prisma
// Modèles clés à implémenter
model Utilisateur { ... }
model Sortie { horizon Horizon; mode_inscription ModeInscription }
model Participation { statut StatutParticipation; ordre_attente Int? }
model Message { ... }
model Localisation { statut_rider StatutRider; visible Boolean }
model Etape { ordre Int }
```

---

### MC-004 — API Auth — Inscription
**Phase :** 1 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Route POST /api/auth/register. Validation des inputs avec Zod, vérification unicité email, hashage bcrypt, création en BDD, retour du JWT.

**Critères d'acceptation :**
- [ ] Validation Zod : email valide, password min 8 chars, prénom requis
- [ ] Email en minuscule avant stockage
- [ ] Password hashé bcrypt (coût 12)
- [ ] Retourne access token (15min) + refresh token en httpOnly cookie (7j)
- [ ] Erreur 409 si email déjà utilisé
- [ ] Erreur 400 si inputs invalides avec message explicite

**Scénarios de test :**
| Scénario | Entrée | Résultat attendu |
|---|---|---|
| Nominal | Email valide + password fort | 201 + tokens |
| Email déjà pris | Email existant | 409 Conflict |
| Password faible | Password < 8 chars | 400 Bad Request |
| Email invalide | "pas-un-email" | 400 Bad Request |

---

### MC-005 — API Auth — Connexion
**Phase :** 1 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Route POST /api/auth/login. Vérification email + password, génération JWT access token + refresh token httpOnly cookie.

**Critères d'acceptation :**
- [ ] Vérification bcrypt du password
- [ ] Access token JWT signé (payload : id, email, role)
- [ ] Refresh token stocké en BDD (hash) + httpOnly cookie
- [ ] Erreur 401 générique si email ou password incorrect (pas de distinction)
- [ ] Rate limiting : 5 tentatives max par 15 minutes par IP

---

### MC-006 — API Auth — Refresh token
**Phase :** 1 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** S | **Couche :** Backend

**Description :**
Route POST /api/auth/refresh. Lit le refresh token dans le cookie httpOnly, vérifie sa validité en BDD, génère un nouvel access token.

**Critères d'acceptation :**
- [ ] Lit le cookie `refreshToken`
- [ ] Vérifie que le token existe en BDD et n'est pas révoqué
- [ ] Retourne un nouvel access token
- [ ] Erreur 401 si token absent, expiré ou révoqué
- [ ] Rotation du refresh token (révoque l'ancien, crée un nouveau)

---

### MC-007 — API Auth — Déconnexion
**Phase :** 1 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** XS | **Couche :** Backend

**Description :**
Route POST /api/auth/logout. Révocation du refresh token en BDD et suppression du cookie.

**Critères d'acceptation :**
- [ ] Révoque le refresh token en BDD
- [ ] Supprime le cookie httpOnly
- [ ] Retourne 200 même si pas de cookie (idempotent)

---

### MC-008 — Middleware d'authentification
**Phase :** 1 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** S | **Couche :** Backend

**Description :**
Middleware Express vérifiant le JWT sur les routes protégées. Injecte l'utilisateur dans req.user.

**Critères d'acceptation :**
- [ ] Lit le token dans le header Authorization (Bearer)
- [ ] Vérifie la signature et l'expiration
- [ ] Injecte `req.user = { id, email, role }`
- [ ] Erreur 401 si token absent ou invalide
- [ ] Erreur 403 si token valide mais rôle insuffisant (middleware role)

---

### MC-009 — API Auth — Réinitialisation mot de passe
**Phase :** 1 | **Bloc :** BC01 | **Priorité :** 🟡 | **Effort :** M | **Couche :** Backend

**Description :**
Route POST /api/auth/forgot-password (envoi email avec lien de reset) et POST /api/auth/reset-password (nouveau password).

**Critères d'acceptation :**
- [ ] Token de reset unique, hashé en BDD, expire dans 1h
- [ ] Email envoyé via Nodemailer avec lien de reset
- [ ] Route de reset vérifie le token et sa validité
- [ ] Token invalidé après utilisation
- [ ] Réponse identique si email inexistant (évite l'énumération)

---

### MC-010 — Frontend — Setup React + Vite + Tailwind
**Phase :** 1 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** S | **Couche :** Frontend

**Description :**
Initialiser le projet React/Vite, configurer Tailwind avec les tokens de la charte MotoConnect, mettre en place React Router v6 et la structure des dossiers.

**Critères d'acceptation :**
- [ ] Vite + React opérationnel
- [ ] Tailwind configuré avec palette custom (--asp, --red, --t1...)
- [ ] React Router v6 avec routes de base
- [ ] Structure : /components, /pages, /hooks, /services, /context, /assets
- [ ] Axios configuré avec intercepteurs (refresh automatique)

---

### MC-011 — Frontend — Pages Auth
**Phase :** 1 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Frontend

**Description :**
Pages Inscription et Connexion avec formulaires validés, gestion des erreurs API, redirection post-login.

**Critères d'acceptation :**
- [ ] Formulaire inscription : prénom, email, password, confirmation
- [ ] Formulaire connexion : email, password
- [ ] Validation côté client (React Hook Form + Zod)
- [ ] Affichage des erreurs API (email déjà pris, mot de passe incorrect)
- [ ] Redirection vers le fil des sorties après connexion
- [ ] Lien "Mot de passe oublié"

---

### MC-012 — Frontend — Contexte Auth global
**Phase :** 1 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** S | **Couche :** Frontend

**Description :**
Context React pour gérer l'état d'authentification globalement. PrivateRoute pour protéger les pages authentifiées.

**Critères d'acceptation :**
- [ ] AuthContext avec user, isAuthenticated, login, logout
- [ ] Persistance de la session (refresh token au reload)
- [ ] PrivateRoute redirige vers /login si non authentifié
- [ ] AdminRoute vérifie le rôle admin

---

### MC-013 — API Profil — CRUD
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Routes GET /api/users/:id et PATCH /api/users/:id pour consulter et modifier un profil rider.

**Critères d'acceptation :**
- [ ] GET retourne les infos publiques (pas le password, pas le refresh token)
- [ ] PATCH autorisé uniquement pour l'utilisateur lui-même ou un admin
- [ ] Champs modifiables : prénom, bio, style_ride, moto, telephone
- [ ] Validation Zod sur le PATCH
- [ ] Historique des sorties inclus dans le GET (sorties organisées + rejointes)

---

### MC-014 — API Profil — Upload avatar Cloudinary
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🟡 | **Effort :** M | **Couche :** Backend

**Description :**
Route POST /api/users/:id/avatar. Upload image via Multer, stockage sur Cloudinary, sauvegarde du public_id en BDD.

**Critères d'acceptation :**
- [ ] Multer configuré (mémoire, taille max 5MB, types : jpg/png/webp)
- [ ] Upload vers Cloudinary (dossier motoconnect/avatars)
- [ ] Ancien avatar supprimé de Cloudinary avant remplacement
- [ ] public_id stocké en BDD, URL construite via SDK Cloudinary
- [ ] Erreur 413 si fichier trop grand
- [ ] Erreur 415 si type non supporté

---

### MC-015 — Frontend — Page profil
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🟡 | **Effort :** M | **Couche :** Frontend

**Description :**
Page profil rider avec affichage des infos, formulaire d'édition inline et upload d'avatar.

**Critères d'acceptation :**
- [ ] Affichage : avatar, prénom, moto, style, bio, stats (nb sorties)
- [ ] Bouton Modifier → formulaire d'édition
- [ ] Upload avatar avec preview avant envoi
- [ ] Historique des sorties organisées et rejointes
- [ ] Vue publique (autre rider) vs vue privée (son propre profil)

---

### MC-016 — API Sortie — Créer
**Phase :** 2 | **Bloc :** BC01 BC02 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Route POST /api/sorties. Validation complète, création en BDD avec les étapes optionnelles.

**Critères d'acceptation :**
- [ ] Champs requis : titre, horizon, lieu_depart, places_max, mode_inscription, style
- [ ] Champs optionnels : description, etapes (tableau de waypoints)
- [ ] Validation : places_max entre 2 et 50, horizon valide
- [ ] Si horizon = MAINTENANT, expire_at calculé automatiquement (now + 2h)
- [ ] Organisateur = utilisateur connecté (depuis req.user)
- [ ] Etapes créées en cascade avec ordre auto-incrémenté

---

### MC-017 — API Sortie — Lister (fil)
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Route GET /api/sorties. Filtres, tri, pagination. Exclut les sorties expirées et annulées.

**Critères d'acceptation :**
- [ ] Filtres : horizon, style, statut, rayon (lat/lng/distance)
- [ ] Tri par défaut : urgence temporelle (MAINTENANT → AUJOURD_HUI → PLANIFIE)
- [ ] Pagination (limit/offset ou cursor)
- [ ] Inclut le nombre de participants confirmés et les places restantes
- [ ] Exclut les sorties expirées (expire_at < now)
- [ ] Exclut les sorties annulées

---

### MC-018 — API Sortie — Détail
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** S | **Couche :** Backend

**Description :**
Route GET /api/sorties/:id. Retourne les données complètes d'une sortie.

**Critères d'acceptation :**
- [ ] Infos sortie + organisateur (profil public)
- [ ] Liste des participants confirmés (profil public)
- [ ] Nombre en liste d'attente (sans détail si non-participant)
- [ ] Etapes de l'itinéraire
- [ ] Statut de participation de l'utilisateur connecté (si authentifié)

---

### MC-019 — API Sortie — Modifier / Annuler
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🟡 | **Effort :** M | **Couche :** Backend

**Description :**
Routes PATCH et DELETE /api/sorties/:id. Réservé à l'organisateur ou un admin.

**Critères d'acceptation :**
- [ ] Vérification que req.user.id === sortie.id_organisateur (ou role admin)
- [ ] PATCH : modification des champs autorisés (pas le mode_inscription si participants)
- [ ] DELETE : statut passe à ANNULEE (soft delete), pas de suppression physique
- [ ] Notification créée pour chaque participant confirmé si annulation

---

### MC-020 — API Inscription — S'inscrire
**Phase :** 2 | **Bloc :** BC01 BC02 | **Priorité :** 🔴 | **Effort :** L | **Couche :** Backend

**Description :**
Route POST /api/sorties/:id/participations. Logique métier complexe : mode auto vs manuel, gestion de la liste d'attente.

**Critères d'acceptation :**
- [ ] Vérifie que l'utilisateur n'est pas déjà inscrit
- [ ] Vérifie que l'utilisateur n'est pas l'organisateur
- [ ] Mode AUTO : statut = CONFIRME si places disponibles, LISTE_ATTENTE sinon
- [ ] Mode MANUEL : statut = EN_ATTENTE toujours, ordre_attente calculé
- [ ] Si EN_ATTENTE : ordre_attente = max(ordre_attente) + 1
- [ ] Notification créée pour l'organisateur (mode manuel)
- [ ] Notification créée pour le rider (confirmation immédiate si mode auto)

---

### MC-021 — API Inscription — Gérer les demandes
**Phase :** 2 | **Bloc :** BC01 BC02 | **Priorité :** 🔴 | **Effort :** L | **Couche :** Backend

**Description :**
Routes PATCH /api/participations/:id/accept et /api/participations/:id/reject. Vue organisateur uniquement.

**Critères d'acceptation :**
- [ ] Seul l'organisateur de la sortie peut accepter/refuser
- [ ] Accepter : statut = CONFIRME, décrémente places disponibles
- [ ] Refuser : statut = REFUSE, notification au rider
- [ ] Si acceptation et liste d'attente : réorganise les ordres
- [ ] Si la sortie est pleine : erreur 409 sur acceptation
- [ ] Notification créée pour le rider dans les deux cas

---

### MC-022 — API Inscription — Se désinscrire
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Route DELETE /api/sorties/:id/participations. Libère une place, promeut le suivant en liste d'attente.

**Critères d'acceptation :**
- [ ] Suppression de la participation (soft delete : statut = ANNULE)
- [ ] Si était CONFIRME : libère une place
- [ ] Si places libérées et liste d'attente : promeut automatiquement le premier en attente (mode auto)
- [ ] Si mode manuel et liste d'attente : notifie l'organisateur
- [ ] Réorganise les ordres_attente des suivants

---

### MC-023 — Frontend — Fil des sorties
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** L | **Couche :** Frontend

**Description :**
Page principale. Liste des sorties triées par horizon avec filtres. Écran d'accueil après connexion.

**Critères d'acceptation :**
- [ ] Séparateurs de sections : Maintenant / Aujourd'hui / Planifié
- [ ] Badge coloré par horizon (rouge live, amber aujourd'hui, gris planifié)
- [ ] Filtres : horizon, style de ride
- [ ] Chaque carte : titre, organisateur, places restantes, heure
- [ ] Bouton FAB "Publier une sortie"
- [ ] Navigation : Fil / Carte / Mes sorties / Profil
- [ ] Pip de notification sur "Mes sorties"
- [ ] Infinite scroll ou pagination

---

### MC-024 — Frontend — Détail d'une sortie
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** L | **Couche :** Frontend

**Description :**
Page détail avec toutes les informations, mini-carte Leaflet, slots participants et actions.

**Critères d'acceptation :**
- [ ] Header avec titre, date/heure, bouton retour et partage
- [ ] Badge organisateur + sa moto
- [ ] Pills : style, niveau, mode d'inscription
- [ ] Mini-carte Leaflet avec étapes colorées
- [ ] Liste des étapes en vertical
- [ ] Avatars des participants + slots vides
- [ ] CTA "Demander à rejoindre" ou statut de ma participation
- [ ] Boutons export GPS (Waze / Google Maps / Apple Maps)
- [ ] Vue organisateur : lien vers gestion des demandes

---

### MC-025 — Frontend — Formulaire créer une sortie
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** L | **Couche :** Frontend

**Description :**
Formulaire multi-étapes : horizon → infos → itinéraire → paramètres → confirmation.

**Critères d'acceptation :**
- [ ] Étape 1 : choix de l'horizon (3 boutons visuels)
- [ ] Étape 2 : titre, lieu de départ, style, description
- [ ] Étape 3 : ajout d'étapes optionnelles (nom + position)
- [ ] Étape 4 : places max, mode d'inscription
- [ ] Étape 5 : récapitulatif avant publication
- [ ] Validation à chaque étape avant de continuer
- [ ] Sauvegarde locale en cas de fermeture accidentelle

---

### MC-026 — Frontend — Gestion des demandes (organisateur)
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Frontend

**Description :**
Page vue organisateur : demandes en attente + participants confirmés + liste d'attente.

**Critères d'acceptation :**
- [ ] Section "Demandes en attente" avec profil du rider (moto, style, nb sorties)
- [ ] Boutons accepter ✓ / refuser ✗ par demande
- [ ] Section "Confirmés" avec point de couleur par statut
- [ ] Section "Liste d'attente" avec ordre numéroté
- [ ] Mise à jour en temps réel (WebSocket ou polling)

---

### MC-027 — Frontend — Mes sorties
**Phase :** 2 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Frontend

**Description :**
Page "Mes sorties" : sorties organisées et sorties où je suis inscrit, avec leurs statuts.

**Critères d'acceptation :**
- [ ] Onglets : "J'organise" / "Je participe"
- [ ] Statuts visibles : confirmé / en attente / annulé
- [ ] Sorties passées vs à venir
- [ ] Lien rapide vers le chat de chaque sortie
- [ ] Bouton "Gérer" sur les sorties organisées

---

### MC-028 — Backend — Setup Socket.io
**Phase :** 3 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Initialiser Socket.io sur le serveur Express. Authentification via token sur la connexion WebSocket. Rooms par sortie.

**Critères d'acceptation :**
- [ ] Socket.io attaché au serveur HTTP
- [ ] Middleware d'auth : vérifie le JWT au handshake
- [ ] Rooms nommées par sortie : `sortie:{id}`
- [ ] Rooms nommées par utilisateur : `user:{id}` (notifications personnelles)
- [ ] Gestion des déconnexions / reconnexions

---

### MC-029 — Backend — Chat — Messages
**Phase :** 3 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Événements Socket.io pour l'envoi et la réception de messages. Persistance en BDD. Messages système automatiques.

**Critères d'acceptation :**
- [ ] Événement `message:send` → vérifie participation → sauvegarde → broadcast `message:receive`
- [ ] Accès réservé aux participants confirmés
- [ ] Messages système créés automatiquement (rider rejoint, sortie annulée)
- [ ] Route GET /api/sorties/:id/messages pour l'historique (pagination)
- [ ] Contenu sanitisé avant stockage (pas de XSS)

---

### MC-030 — Frontend — Chat de sortie
**Phase :** 3 | **Bloc :** BC01 | **Priorité :** 🔴 | **Effort :** L | **Couche :** Frontend

**Description :**
Interface chat avec bulles, messages système et input d'envoi.

**Critères d'acceptation :**
- [ ] Chargement de l'historique au montage
- [ ] Scroll automatique vers le bas sur nouveau message
- [ ] Bulles gauche (autres) / droite (moi)
- [ ] Messages système centrés en pill
- [ ] Indicateur de saisie (typing indicator)
- [ ] Connexion Socket.io avec reconnexion automatique
- [ ] Input désactivé si non-participant confirmé

---

### MC-031 — Backend — Géolocalisation temps réel
**Phase :** 3 | **Bloc :** BC01 | **Priorité :** 🟡 | **Effort :** L | **Couche :** Backend

**Description :**
Événements Socket.io pour la mise à jour de la position des riders. Stockage temporaire en Redis ou BDD.

**Critères d'acceptation :**
- [ ] Événement `location:update` → met à jour la position en BDD (table LOCALISATION)
- [ ] Broadcast `location:riders` → envoie les positions des riders visibles
- [ ] Filtrage : uniquement les riders avec visible = true
- [ ] Statut_rider : disponible / en_balade / propose_sortie
- [ ] Nettoyage des positions après 5 minutes d'inactivité
- [ ] Rate limiting : max 1 update / 3 secondes par rider

---

### MC-032 — Backend — Carte — API positions
**Phase :** 3 | **Bloc :** BC01 | **Priorité :** 🟡 | **Effort :** S | **Couche :** Backend

**Description :**
Route GET /api/carte pour charger les données initiales de la carte (sorties + positions riders).

**Critères d'acceptation :**
- [ ] Retourne les sorties actives avec leurs coordonnées de départ
- [ ] Retourne les positions des riders visibles (mis à jour récemment)
- [ ] Filtrable par rayon (lat, lng, radius en km)
- [ ] Pas de données sensibles exposées (email, téléphone)

---

### MC-033 — Frontend — Carte interactive
**Phase :** 3 | **Bloc :** BC01 | **Priorité :** 🟡 | **Effort :** XL | **Couche :** Frontend

**Description :**
Page carte avec Leaflet. Markers pour les sorties et les riders. Mise à jour temps réel via Socket.io.

**Critères d'acceptation :**
- [ ] Carte Leaflet centrée sur la position de l'utilisateur
- [ ] Markers de sorties (cliquables → bottom sheet avec info sortie)
- [ ] Markers de riders avec icône et statut
- [ ] Mise à jour temps réel des positions (Socket.io)
- [ ] Bouton "Mode invisible" pour ne plus apparaître
- [ ] Envoi de sa propre position toutes les 3 secondes (si visible)
- [ ] Clustering des markers si zoom faible

---

### MC-034 — Backend — Notifications
**Phase :** 3 | **Bloc :** BC01 | **Priorité :** 🟡 | **Effort :** M | **Couche :** Backend

**Description :**
Système de notifications en BDD + push via Socket.io. Emails via Nodemailer sur les événements clés.

**Critères d'acceptation :**
- [ ] Table Notification créée (type, contenu, lu, id_user, lien)
- [ ] Push Socket.io sur la room `user:{id}` pour notifs temps réel
- [ ] Route GET /api/notifications (avec pagination, filtre lu/non-lu)
- [ ] Route PATCH /api/notifications/:id/read
- [ ] Route PATCH /api/notifications/read-all
- [ ] Email : inscription acceptée, sortie annulée, place libérée

---

### MC-035 — Frontend — Centre de notifications
**Phase :** 3 | **Bloc :** BC01 | **Priorité :** 🟡 | **Effort :** M | **Couche :** Frontend

**Description :**
Badge de notifications dans la nav + dropdown ou page dédiée avec liste des notifs.

**Critères d'acceptation :**
- [ ] Badge rouge sur l'icône nav avec le nombre de non-lues
- [ ] Dropdown ou page : liste des notifications récentes
- [ ] Clic sur une notif → marque comme lue + redirige vers la ressource
- [ ] Bouton "Tout marquer comme lu"
- [ ] Mise à jour en temps réel via Socket.io

---

### MC-036 — Sécurité — Audit OWASP
**Phase :** 4 | **Bloc :** BC01 BC03 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Vérifier les 10 vulnérabilités OWASP Top 10 et documenter les mesures prises.

**Critères d'acceptation :**
- [ ] A01 Broken Access Control : vérification des droits sur chaque route
- [ ] A02 Cryptographic Failures : HTTPS, bcrypt, tokens signés
- [ ] A03 Injection : Prisma protège contre SQL injection
- [ ] A04 Insecure Design : validation Zod sur tous les inputs
- [ ] A07 Auth Failures : rate limiting sur /auth, refresh token rotation
- [ ] A09 Logging : logs structurés, pas de données sensibles loggées
- [ ] Helmet configuré (headers HTTP sécurisés)
- [ ] CORS configuré (origines autorisées uniquement)
- [ ] Document de synthèse sécurité rédigé pour le dossier CDA

---

### MC-037 — Tests — Auth
**Phase :** 4 | **Bloc :** BC03 | **Priorité :** 🔴 | **Effort :** M | **Couche :** Backend

**Description :**
Tests Jest + Supertest sur toutes les routes /auth.

**Critères d'acceptation :**
- [ ] Tests inscription : cas nominal, email existant, inputs invalides
- [ ] Tests connexion : cas nominal, mauvais password, compte inexistant
- [ ] Tests refresh : token valide, token expiré, token révoqué
- [ ] Tests logout : cas nominal, sans cookie
- [ ] Couverture > 80% sur le module auth

---

### MC-038 — Tests — Sorties et Inscriptions
**Phase :** 4 | **Bloc :** BC03 | **Priorité :** 🔴 | **Effort :** L | **Couche :** Backend

**Description :**
Tests Jest + Supertest sur les routes /sorties et /participations.

**Critères d'acceptation :**
- [ ] Tests CRUD sorties : création, liste, détail, modification, annulation
- [ ] Tests droits : un rider ne peut pas modifier la sortie d'un autre
- [ ] Tests inscription mode auto : places dispo / liste d'attente
- [ ] Tests inscription mode manuel : flux complet acceptation/refus
- [ ] Tests désinscription : libération de place + promotion liste d'attente
- [ ] Couverture > 70% sur les modules sorties et participations

---

### MC-039 — DevOps — Dockerfile production
**Phase :** 5 | **Bloc :** BC03 | **Priorité :** 🔴 | **Effort :** M | **Couche :** DevOps

**Description :**
Dockerfiles de production pour le backend et le frontend.

**Critères d'acceptation :**
- [ ] Backend : Dockerfile multi-stage (build → prod), image node:alpine
- [ ] Frontend : Dockerfile build React + Nginx pour servir les statiques
- [ ] Images < 200MB
- [ ] Variables d'environnement externalisées (pas hardcodées)
- [ ] .dockerignore configuré

---

### MC-040 — DevOps — CI/CD GitHub Actions
**Phase :** 5 | **Bloc :** BC03 | **Priorité :** 🔴 | **Effort :** M | **Couche :** DevOps

**Description :**
Pipeline GitHub Actions : lint → tests → build → déploiement automatique.

**Critères d'acceptation :**
- [ ] Trigger : push sur develop et pull request vers main
- [ ] Job lint : ESLint + Prettier
- [ ] Job test : Jest avec rapport de couverture
- [ ] Job build : build Docker et push sur registry
- [ ] Job deploy : déploiement automatique sur Railway (develop) et prod (main)
- [ ] Notifications en cas d'échec

---

### MC-041 — DevOps — Déploiement production
**Phase :** 5 | **Bloc :** BC03 | **Priorité :** 🔴 | **Effort :** M | **Couche :** DevOps

**Description :**
Déploiement complet : Neon (BDD) + Railway (backend) + Vercel (frontend).

**Critères d'acceptation :**
- [ ] BDD PostgreSQL sur Neon avec migration Prisma appliquée
- [ ] Backend Node.js sur Railway avec variables d'environnement configurées
- [ ] Frontend React sur Vercel avec VITE_API_URL configurée
- [ ] HTTPS activé sur tous les services
- [ ] Domaine custom si possible
- [ ] Vérification que le WebSocket fonctionne en production

---

### MC-042 — Dossier CDA — Rédaction
**Phase :** 6 | **Bloc :** BC01 BC02 BC03 | **Priorité :** 🔴 | **Effort :** XL | **Couche :** —

**Description :**
Rédiger le dossier de projet complet selon la structure attendue par le jury CDA.

**Critères d'acceptation :**
- [ ] Présentation du contexte et des besoins utilisateurs (personas)
- [ ] Conception : MCD, UML, wireframes, architecture
- [ ] Développement : stack justifiée, extraits de code commentés
- [ ] Sécurité : OWASP, RGPD, mesures mises en place
- [ ] Tests : plan de tests, résultats, couverture
- [ ] Déploiement : guide complet, CI/CD, architecture infra
- [ ] Bilan : difficultés rencontrées, ce que j'ai appris, améliorations possibles
- [ ] Export PDF soigné

---

### MC-043 — Dossier CDA — Diaporama
**Phase :** 6 | **Bloc :** BC01 BC02 BC03 | **Priorité :** 🔴 | **Effort :** L | **Couche :** —

**Description :**
Préparer le support de présentation (diaporama) pour les 40 minutes devant le jury.

**Critères d'acceptation :**
- [ ] 15-20 slides maximum
- [ ] Fil conducteur : problème → solution → démo → architecture → retour critique
- [ ] Captures d'écran de l'app fonctionnelle
- [ ] Schémas MCD et architecture lisibles
- [ ] Export PDF

---

*MotoConnect — Template tickets Notion · Licence CDA 2025/2026*
