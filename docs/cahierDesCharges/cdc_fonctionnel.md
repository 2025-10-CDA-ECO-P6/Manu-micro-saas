# Kruse — Cahier des charges fonctionnel
**Licence Concepteur Développeur d'Application — 2025/2026**

---

## La fonctionnalité principale — Le fil des sorties

Le cœur de Kruse est le **fil des sorties** : un flux de sorties organisées en trois horizons temporels, triées par urgence, consultable par tous les riders connectés.

Un rider peut publier une sortie en renseignant :
- L'horizon (maintenant / aujourd'hui / planifié)
- Le lieu de départ
- Le style de ride (sportif, peinard, col, touring)
- Le nombre de places disponibles
- Le mode d'inscription (automatique ou validation manuelle)

La sortie apparaît immédiatement dans le fil des autres riders. C'est cette fonctionnalité qui répond directement à la problématique : rendre visible en temps réel l'intention de rouler, pour n'importe quel horizon temporel.

---

## Fonctionnalités secondaires

### Authentification et profil
Un rider crée un compte avec ses informations : prénom, moto (marque, modèle, année), style de ride préféré, et une photo de profil. Ces informations sont visibles par les autres riders avant de s'inscrire à une sortie — c'est ce qui permet de juger la compatibilité d'un groupe.

### Inscription à une sortie
Deux modes d'inscription coexistent. En mode automatique, la place est réservée immédiatement si des places sont disponibles. En mode manuel, l'organisateur reçoit une demande et choisit d'accepter ou de refuser — une liste d'attente ordonnée gère les cas où toutes les places sont prises.

### Gestion des demandes (vue organisateur)
L'organisateur voit le profil de chaque demandeur (moto, style, historique des sorties) avant de valider. Il gère sa liste de participants et sa liste d'attente depuis une interface dédiée.

### Itinéraire et export GPS
Une sortie peut inclure des étapes (waypoints) affichées sur une mini-carte Leaflet. L'itinéraire est exportable en un tap vers Waze, Google Maps ou Apple Maps.

### Chat de sortie
Chaque sortie dispose d'un fil de discussion accessible uniquement aux participants confirmés. Des messages système automatiques informent le groupe des événements clés (nouveau participant, annulation de sortie).

### Carte interactive
Une carte Leaflet affiche les sorties actives géolocalisées. En complément, la position des riders est partagée en temps réel via WebSocket pour les riders qui activent cette option. Un mode invisible permet de ne pas apparaître sur la carte.

### Notifications
Un système de notifications en application informe les riders des événements les concernant : inscription acceptée, sortie annulée, place libérée en liste d'attente. Des emails sont envoyés sur les événements critiques.

### Administration
Un rôle administrateur permet de modérer le contenu et de gérer les utilisateurs. L'admin se connecte via la même interface que les riders — son rôle est différencié par le JWT.

---

## Hors scope MVP

Les éléments suivants ne font pas partie du MVP :
- Application mobile native (web uniquement)
- Système de notation ou d'avis sur les riders
- Paiement ou abonnement
- Intégration réseaux sociaux (login Google/Facebook)
- Fil d'actualité communautaire (posts, photos)
- Calcul automatique d'itinéraire (Directions API)
- Détection de chute

---

*Kruse — Cahier des charges fonctionnel v1.0 · Licence CDA 2025/2026*
