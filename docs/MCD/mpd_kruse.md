# Kruse — Modèle Physique de Données (MPD)
## Schéma Prisma — PostgreSQL 16

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// ─────────────────────────────────────────
// ENUMS
// ─────────────────────────────────────────

enum Role {
  RIDER
  ADMIN
}

enum StyleRide {
  SPORTIF
  PEINARD
  COL
  TOURING
}

enum Horizon {
  MAINTENANT
  AUJOURD_HUI
  PLANIFIE
}

enum ModeInscription {
  AUTOMATIQUE
  MANUEL
}

enum StatutSortie {
  PLANIFIEE
  IMMINENTE
  EN_COURS
  TERMINEE
  ANNULEE
}

enum PhotoSource {
  UNSPLASH
  CUSTOM
}

enum StatutParticipation {
  CONFIRME
  EN_ATTENTE
  REFUSE
  ANNULE
}

enum TypeMessage {
  TEXTE
  SYSTEME
}

enum StatutRider {
  DISPONIBLE
  EN_BALADE
  PROPOSE_SORTIE
}

enum TypeNotification {
  INSCRIPTION_ACCEPTEE
  INSCRIPTION_REFUSEE
  PLACE_LIBEREE
  SORTIE_ANNULEE
  NOUVEAU_MESSAGE
}

// ─────────────────────────────────────────
// MODÈLES
// ─────────────────────────────────────────

model Utilisateur {
  id           String    @id @default(uuid()) @db.Uuid
  prenom       String    @db.VarChar(50)
  nom          String    @db.VarChar(50)
  email        String    @unique @db.VarChar(255)
  motDePasse   String    @db.VarChar(255)
  role         Role      @default(RIDER)
  avatar       String?   @db.VarChar(500)
  telephone    String?   @db.VarChar(20)
  styleRide    StyleRide?
  moto         String?   @db.VarChar(150)
  createdAt    DateTime  @default(now())

  // Relations
  sorties       Sortie[]        @relation("Organisateur")
  participations Participation[]
  messages      Message[]
  localisation  Localisation?
  notifications Notification[]
  refreshTokens RefreshToken[]

  @@map("utilisateur")
}

model Sortie {
  id              String          @id @default(uuid()) @db.Uuid
  titre           String          @db.VarChar(150)
  horizon         Horizon
  style           StyleRide
  lieuDepart      String          @db.VarChar(200)
  latDepart       Decimal         @db.Decimal(10, 7)
  lngDepart       Decimal         @db.Decimal(10, 7)
  placesMax       Int
  modeInscription ModeInscription
  statut          StatutSortie    @default(PLANIFIEE)
  expireAt        DateTime?
  photoUrl        String?         @db.VarChar(500)
  photoSource     PhotoSource?
  description     String?         @db.Text
  createdAt       DateTime        @default(now())
  idOrganisateur  String          @db.Uuid

  // Relations
  organisateur   Utilisateur     @relation("Organisateur", fields: [idOrganisateur], references: [id])
  participations Participation[]
  messages       Message[]
  etapes         Etape[]

  // Index
  @@index([statut, horizon])
  @@index([idOrganisateur])
  @@map("sortie")
}

model Participation {
  id           String              @id @default(uuid()) @db.Uuid
  statut       StatutParticipation
  ordreAttente Int?
  createdAt    DateTime            @default(now())
  idUser       String              @db.Uuid
  idSortie     String              @db.Uuid

  // Relations
  utilisateur Utilisateur @relation(fields: [idUser], references: [id], onDelete: Cascade)
  sortie      Sortie      @relation(fields: [idSortie], references: [id], onDelete: Cascade)

  // Contraintes
  @@unique([idUser, idSortie])
  @@index([idUser, statut])
  @@index([idSortie, statut])
  @@map("participation")
}

model Message {
  id        String      @id @default(uuid()) @db.Uuid
  contenu   String      @db.Text
  type      TypeMessage @default(TEXTE)
  createdAt DateTime    @default(now())
  idUser    String      @db.Uuid
  idSortie  String      @db.Uuid

  // Relations
  utilisateur Utilisateur @relation(fields: [idUser], references: [id], onDelete: SetNull)
  sortie      Sortie      @relation(fields: [idSortie], references: [id], onDelete: Cascade)

  // Index
  @@index([idSortie, createdAt])
  @@map("message")
}

model Localisation {
  id          String      @id @default(uuid()) @db.Uuid
  latitude    Decimal     @db.Decimal(10, 7)
  longitude   Decimal     @db.Decimal(10, 7)
  visible     Boolean     @default(true)
  statutRider StatutRider @default(DISPONIBLE)
  updatedAt   DateTime    @updatedAt
  idUser      String      @unique @db.Uuid

  // Relations
  utilisateur Utilisateur @relation(fields: [idUser], references: [id], onDelete: Cascade)

  // Index
  @@index([updatedAt])
  @@map("localisation")
}

model Etape {
  id        String  @id @default(uuid()) @db.Uuid
  label     String  @db.VarChar(150)
  latitude  Decimal @db.Decimal(10, 7)
  longitude Decimal @db.Decimal(10, 7)
  ordre     Int
  idSortie  String  @db.Uuid

  // Relations
  sortie Sortie @relation(fields: [idSortie], references: [id], onDelete: Cascade)

  @@map("etape")
}

model Notification {
  id        String           @id @default(uuid()) @db.Uuid
  type      TypeNotification
  contenu   String           @db.VarChar(255)
  lu        Boolean          @default(false)
  lien      String?          @db.VarChar(500)
  createdAt DateTime         @default(now())
  idUser    String           @db.Uuid

  // Relations
  utilisateur Utilisateur @relation(fields: [idUser], references: [id], onDelete: Cascade)

  // Index
  @@index([idUser, lu])
  @@map("notification")
}

model RefreshToken {
  id        String   @id @default(uuid()) @db.Uuid
  token     String   @unique @db.VarChar(500)
  expiresAt DateTime
  createdAt DateTime @default(now())
  idUser    String   @db.Uuid

  utilisateur Utilisateur @relation(fields: [idUser], references: [id], onDelete: Cascade)

  @@map("refresh_token")
}
```

## Commandes Prisma

```bash
# Appliquer les migrations en développement
npx prisma migrate dev --name init

# Générer le client Prisma
npx prisma generate

# Visualiser la BDD
npx prisma studio

# Appliquer en production
npx prisma migrate deploy
```

