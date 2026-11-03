# Kruse — Modèle Logique de Données (MLD)

## Tables et clés

### UTILISATEUR
```
UTILISATEUR (
  id            UUID        PK,
  prenom        VARCHAR(50) NOT NULL,
  nom           VARCHAR(50) NOT NULL,
  email         VARCHAR(255) NOT NULL UNIQUE,
  mot_de_passe  VARCHAR(255) NOT NULL,
  role          ENUM('RIDER', 'ADMIN') NOT NULL DEFAULT 'RIDER',
  avatar        VARCHAR(500),
  telephone     VARCHAR(20),
  style_ride    ENUM('SPORTIF', 'PEINARD', 'COL', 'TOURING'),
  moto          VARCHAR(150),
  created_at    TIMESTAMP NOT NULL DEFAULT NOW()
)
```

### SORTIE
```
SORTIE (
  id               UUID         PK,
  titre            VARCHAR(150) NOT NULL,
  horizon          ENUM('MAINTENANT', 'AUJOURD_HUI', 'PLANIFIE') NOT NULL,
  style            ENUM('SPORTIF', 'PEINARD', 'COL', 'TOURING') NOT NULL,
  lieu_depart      VARCHAR(200) NOT NULL,
  lat_depart       DECIMAL(10,7) NOT NULL,
  lng_depart       DECIMAL(10,7) NOT NULL,
  places_max       INT NOT NULL CHECK (places_max BETWEEN 2 AND 50),
  mode_inscription ENUM('AUTOMATIQUE', 'MANUEL') NOT NULL,
  statut           ENUM('PLANIFIEE', 'IMMINENTE', 'EN_COURS', 'TERMINEE', 'ANNULEE') NOT NULL DEFAULT 'PLANIFIEE',
  expire_at        TIMESTAMP,
  photo_url        VARCHAR(500),
  photo_source     ENUM('UNSPLASH', 'CUSTOM'),
  description      TEXT,
  created_at       TIMESTAMP NOT NULL DEFAULT NOW(),
  id_organisateur  UUID NOT NULL FK → UTILISATEUR(id)
)
```

### PARTICIPATION
```
PARTICIPATION (
  id           UUID PK,
  statut       ENUM('CONFIRME', 'EN_ATTENTE', 'REFUSE', 'ANNULE') NOT NULL,
  ordre_attente INT,
  created_at   TIMESTAMP NOT NULL DEFAULT NOW(),
  id_user      UUID NOT NULL FK → UTILISATEUR(id),
  id_sortie    UUID NOT NULL FK → SORTIE(id),
  UNIQUE (id_user, id_sortie)
)
```

### MESSAGE
```
MESSAGE (
  id         UUID PK,
  contenu    TEXT NOT NULL,
  type       ENUM('TEXTE', 'SYSTEME') NOT NULL DEFAULT 'TEXTE',
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  id_user    UUID NOT NULL FK → UTILISATEUR(id),
  id_sortie  UUID NOT NULL FK → SORTIE(id)
)
```

### LOCALISATION
```
LOCALISATION (
  id           UUID PK,
  latitude     DECIMAL(10,7) NOT NULL,
  longitude    DECIMAL(10,7) NOT NULL,
  visible      BOOLEAN NOT NULL DEFAULT TRUE,
  statut_rider ENUM('DISPONIBLE', 'EN_BALADE', 'PROPOSE_SORTIE') NOT NULL DEFAULT 'DISPONIBLE',
  updated_at   TIMESTAMP NOT NULL DEFAULT NOW(),
  id_user      UUID NOT NULL UNIQUE FK → UTILISATEUR(id)
)
```

### ETAPE
```
ETAPE (
  id        UUID PK,
  label     VARCHAR(150) NOT NULL,
  latitude  DECIMAL(10,7) NOT NULL,
  longitude DECIMAL(10,7) NOT NULL,
  ordre     INT NOT NULL,
  id_sortie UUID NOT NULL FK → SORTIE(id)
)
```

### NOTIFICATION
```
NOTIFICATION (
  id         UUID PK,
  type       ENUM('INSCRIPTION_ACCEPTEE', 'INSCRIPTION_REFUSEE', 'PLACE_LIBEREE', 'SORTIE_ANNULEE', 'NOUVEAU_MESSAGE') NOT NULL,
  contenu    VARCHAR(255) NOT NULL,
  lu         BOOLEAN NOT NULL DEFAULT FALSE,
  lien       VARCHAR(500),
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  id_user    UUID NOT NULL FK → UTILISATEUR(id)
)
```

## Contraintes d'intégrité

- `SORTIE.id_organisateur` → ON DELETE RESTRICT (une sortie ne peut pas exister sans organisateur)
- `PARTICIPATION.id_user` → ON DELETE CASCADE (si l'utilisateur est supprimé, ses participations le sont aussi)
- `PARTICIPATION.id_sortie` → ON DELETE CASCADE (si la sortie est supprimée, les participations aussi)
- `MESSAGE.id_user` → ON DELETE SET NULL (les messages restent mais l'auteur devient anonyme)
- `MESSAGE.id_sortie` → ON DELETE CASCADE
- `LOCALISATION.id_user` → ON DELETE CASCADE (1 localisation par utilisateur)
- `ETAPE.id_sortie` → ON DELETE CASCADE (les étapes sont liées à la sortie)
- `NOTIFICATION.id_user` → ON DELETE CASCADE

## Index recommandés

- `SORTIE(statut, horizon)` — pour le fil des sorties
- `SORTIE(id_organisateur)` — pour "mes sorties organisées"
- `PARTICIPATION(id_user, statut)` — pour "mes sorties rejointes"
- `PARTICIPATION(id_sortie, statut)` — pour la liste des participants
- `MESSAGE(id_sortie, created_at)` — pour le chat paginé
- `NOTIFICATION(id_user, lu)` — pour le centre de notifications
- `LOCALISATION(updated_at)` — pour nettoyer les positions obsolètes

