# Kruse — Organisation & Planification du projet
**Licence Concepteur Développeur d'Application — 2025/2026**

---

## Contexte de travail

Ce projet est réalisé en autonomie dans le cadre d'une formation en alternance. Le temps disponible est contraint et réparti entre les semaines en école, le temps en entreprise et le temps personnel. L'organisation doit donc être flexible, réaliste et adaptée à ce rythme discontinu.

**Répartition du temps :**
- 7 semaines de cours dédiées au projet sur 7 mois
- Temps personnel en dehors de l'alternance (soirs, weekends)
- Temps en entreprise non disponible pour le projet

L'estimation totale de travail effectif est de **200 à 250 heures** sur la durée du projet.

---

## Philosophie d'organisation

L'organisation choisie s'inspire des méthodes agiles sans en appliquer la rigidité. Pas de sprints fixes, pas de cérémonies — mais une discipline sur les tickets, la documentation et le versionnement.

Trois principes guident le travail au quotidien :

**Tout passe par un ticket** — aucune feature, aucun fix, aucune décision technique n'est réalisée sans un ticket correspondant. Cela permet de tracer le travail effectué, de justifier les choix dans le dossier de projet, et de ne jamais perdre le fil.

**Un ticket = une branche** — chaque ticket donne lieu à une branche `feature/nom-du-ticket` sur GitHub. Jamais de code directement sur `develop` ou `main`.

**La documentation est écrite en même temps que le code** — pas après. Chaque feature terminée est documentée avant de passer au ticket suivant.

---

## Structure des phases

Le projet est découpé en 6 phases séquentielles. Chaque phase a un objectif clair et des livrables définis. L'ordre est respecté — on ne commence pas le développement sans avoir terminé la conception.

### Phase 0 — Conception
Objectif : avoir une base de conception solide avant d'écrire la première ligne de code. Tous les livrables de cette phase alimentent directement le dossier de projet CDA.

Livrables :
- Personas (3 profils utilisateurs)
- User stories
- MCD (draw.io + PNG)
- Diagrammes UML (cas d'utilisation global + détaillés)
- Diagrammes de séquence (flux critiques)
- Wireframes (écrans principaux)
- Architecture technique documentée
- Politique RGPD
- Charte graphique validée

### Phase 1 — Fondations techniques
Objectif : avoir un environnement de développement fonctionnel et une base de code propre sur laquelle construire.

Livrables :
- Repo GitHub structuré avec Git Flow
- Docker Compose (PostgreSQL + Adminer)
- Schéma Prisma complet + première migration
- Authentification complète (inscription, connexion, refresh token, logout, reset password)
- Navigation React Native de base (Tab + Stack)
- Écrans d'authentification (inscription, connexion)
- Contexte Auth global

### Phase 2 — Core features
Objectif : le cœur du produit fonctionnel — publier et rejoindre une sortie.

Livrables :
- Profil rider (CRUD + avatar Cloudinary)
- Sorties (créer, lister, détail, modifier, annuler)
- Fil des sorties avec filtres (horizon, style)
- Inscriptions (mode auto + mode manuel)
- Gestion des demandes (accepter, refuser, liste d'attente)
- Itinéraire avec étapes + export GPS
- Mes sorties (organisées + rejointes)

### Phase 3 — Temps réel
Objectif : ajouter la dimension temps réel qui différencie Kruse.

Livrables :
- Chat de sortie (Socket.io, rooms par sortie)
- Notifications en app (centre de notifications)
- Carte interactive (react-native-maps)
- Géolocalisation riders en temps réel (WebSocket)
- Mode invisible
- Notifications push (Expo Notifications)

### Phase 4 — Qualité & Sécurité
Objectif : s'assurer que le projet est robuste, sécurisé et testable avant le déploiement.

Livrables :
- Plan de tests rédigé
- Tests Jest + Supertest (auth, sorties, participations)
- Audit sécurité OWASP (documentation des mesures)
- Validation Zod sur toutes les routes
- Rate limiting + Helmet configurés

### Phase 5 — Déploiement
Objectif : avoir une application déployée et accessible pour la démo du jury.

Livrables :
- Manifests Kubernetes (Minikube staging local)
- Déploiement backend Railway (production)
- Déploiement BDD Neon
- CI/CD GitHub Actions (lint → tests → build → deploy)
- Documentation de déploiement complète
- App fonctionnelle sur iPhone physique

### Phase 6 — Dossier de projet & Oral
Objectif : préparer et livrer tous les documents attendus par le jury CDA.

Livrables :
- Dossier de projet complet (PDF)
- Diaporama de présentation (15-20 slides)
- Préparation de l'entretien technique
- Préparation du questionnaire en anglais

---

## Outils utilisés

### Notion — Documentation & tickets
Notion est l'outil central du projet. Il centralise toute la documentation technique et le suivi des tickets de développement.

**Structure Notion :**
```
Kruse/
├── Cadrage produit
│   ├── MVP Canvas
│   ├── Personas
│   └── User stories
├── Conception
│   ├── MCD
│   ├── Diagrammes UML
│   ├── Wireframes
│   ├── Architecture technique
│   └── RGPD + Charte graphique
├── Tickets (base de données)
│   ├── Vue Kanban (suivi quotidien)
│   ├── Vue par phase (vue d'ensemble)
│   └── Vue par bloc CDA (pour le dossier)
├── Tests
│   ├── Plan de tests
│   └── Résultats
├── Déploiement
│   ├── Guide de déploiement
│   └── Manifests Kubernetes
└── Dossier CDA
    ├── Dossier de projet
    └── Diaporama
```

### Structure d'un ticket Notion
Chaque ticket suit le même format pour garantir la traçabilité :

| Propriété | Valeur |
|---|---|
| ID | MC-XXX |
| Statut | Backlog / Conception / À faire / En cours / Review / Test / Terminé |
| Phase | Phase 0 à Phase 6 |
| Bloc CDA | BC01 / BC02 / BC03 |
| Catégorie | Auth / Profil / Sortie / Chat / Carte / Tests / DevOps... |
| Priorité | Indispensable / Important / Bonus |
| Effort | XS (1h) / S (2-3h) / M (4-6h) / L (1-2j) / XL (3j+) |
| Couche | Frontend / Backend / BDD / DevOps |

Le corps de chaque ticket contient :
- Description du besoin
- Critères d'acceptation (checklist)
- Notes techniques
- Scénarios de test
- Journal des décisions

### GitHub — Versionnement & CI/CD
Le code est versionné sur GitHub avec une stratégie de branches Git Flow simplifiée.

**Branches :**
```
main          ← code de production (protégé)
develop       ← branche d'intégration
feature/xxx   ← une branche par ticket (ex: feature/MC-016-api-sortie-creer)
```

**Règles :**
- Jamais de push direct sur `main` ou `develop`
- Chaque feature part d'une branche `feature/ID-nom` créée depuis `develop`
- Merge dans `develop` une fois le ticket passé en "Terminé" dans Notion
- `main` ne reçoit du code que depuis `develop` quand une phase est complète

**Conventions de commit (Conventional Commits) :**
```
feat(auth): add refresh token rotation
fix(sortie): correct ordre_attente on désinscription
docs(readme): update stack with React Native
test(auth): add inscription edge cases
chore(docker): update postgres version to 16
```

### Xcode — Build iOS
Utilisé uniquement pour compiler et déployer l'app sur l'iPhone physique. Pas d'édition de code dans Xcode — uniquement le build. Le workflow est `npx expo prebuild` → Xcode build → Fast Refresh via Metro pour les modifications du code JS.

---

## Gestion des priorités

Chaque ticket est priorisé selon trois niveaux :

**Indispensable** — bloquant pour la suite ou pour le jury CDA. À traiter en premier, dans l'ordre de la phase.

**Important** — ajoute de la valeur mais ne bloque pas. Traité une fois les indispensables terminés.

**Bonus** — nice-to-have. Traité uniquement si du temps est disponible après les phases 1 à 4.

En cas de manque de temps, les features "Bonus" sont supprimées du scope. Les features "Indispensable" et "Important" constituent le MVP minimal présentable au jury.

---

## Traçabilité pour le dossier CDA

Chaque ticket est tagué avec son bloc CDA (BC01, BC02, BC03). Cela permet, lors de la rédaction du dossier de projet, de retrouver facilement quel travail couvre quelle compétence du référentiel RNCP37873.

La vue Notion "par bloc CDA" permet de générer automatiquement la liste des réalisations par compétence — ce qui structure directement le dossier de projet.

---

*Kruse — Organisation & Planification v1.0 · Licence CDA 2025/2026*
