# HERINO

Projet portfolio fullstack — solution de surveillance de logements à distance.

Permet à des clients de faire surveiller leur logement pendant leurs absences par des agents de terrain, avec une interface web client, un back-office administrateur, et une API REST robuste.

---

## Structure du projet

```
herino/
├── herino-api/     Backend NestJS + Prisma + PostgreSQL
└── herino-web/     Frontend React + TypeScript + Tailwind
```

---

## Stack technique

### Backend (`herino-api`)

- **NestJS** — framework Node.js modulaire
- **TypeScript** — typage strict (`strict: true`)
- **Prisma 7** — ORM avec adapter PostgreSQL
- **PostgreSQL** — base de données relationnelle
- **Docker** — containerisation API + base de données
- **JWT** — authentification stateless

### Frontend (`herino-web`)

- **React 19** — interface utilisateur
- **TypeScript** — typage strict
- **Vite** — bundler
- **React Router v6** — routing
- **TanStack Query** — state serveur et cache
- **Zustand** — state client (session)
- **Tailwind CSS + shadcn/ui** — design system

---

## Fonctionnalités

### Côté client

- Inscription et connexion
- Gestion de plusieurs logements
- Création de périodes de surveillance avec choix des créneaux
- Estimateur de prix avant inscription (route publique)
- Demande de passage d'urgence 24/7
- Consultation des passages et alertes

### Côté agent (mobile)

- Activation/désactivation du statut
- Accès à la tournée du créneau en cours
- Validation des passages avec compte-rendu
- Gestion des urgences prioritaires

### Côté administrateur

- Gestion des clients et agents
- Suivi des tournées, alertes et urgences
- Configuration des tarifs et infos société
- Vue des surveillances avec option télésurveillance

---

## Architecture

```
User (CLIENT | AGENT | ADMIN)
  └── Property[]
        └── Surveillance[]
              └── Passage[]
                    └── PassageReport[]
        └── Alert[]
Round → Passage[]
```

**Rôles et accès**

- `CLIENT` — gestion de ses logements et surveillances
- `AGENT` — accès aux tournées et validation des passages
- `ADMIN` — back-office complet, configuration

---

## Lancement rapide

### Prérequis

- Node.js 20+
- Docker et Docker Compose

### Backend

```bash
cd herino-api
cp .env.example .env   # configurer les variables
chmod +x deploy.sh
./deploy.sh            # build Docker + migrations + seed
```

L'API démarre sur `http://localhost:3000`.

Credentials admin par défaut :

```
Email    : admin@herino.fr
Password : défini dans ADMIN_DEFAULT_PASSWORD
```

### Frontend

```bash
cd herino-web
cp .env.example .env   # définir VITE_API_URL
npm install
npm run dev
```

L'app démarre sur `http://localhost:5173`.

Par défaut, `VITE_API_URL` pointe sur `http://localhost:3000`.

---

## Documentation détaillée

- [herino-api/README.md](./herino-api/README.md) — routes API, variables d'environnement, architecture backend
- [herino-web/README.md](./herino-web/README.md) — stack frontend, variables d'environnement, structure et routing

---

## Choix techniques notables

**Feature-based architecture** — chaque module est autonome avec ses controllers, services et DTOs. Inspiré de l'approche NestJS côté backend, et reproduit côté frontend avec une séparation services / hooks queries / hooks métier / composants.

**TypeScript strict bout en bout** — `strict: true` activé sur les deux projets, types Prisma explicites avec `GetPayload`, aucun `any`.

**Séparation state serveur / state client** — TanStack Query pour les données API (cache, refetch, loading), Zustand uniquement pour la session utilisateur (token JWT).

**PassageReport** — un passage peut être visité plusieurs fois dans un même créneau. Chaque visite génère un `PassageReport` tracé en base, permettant à l'agent de repasser et de reporter sans écraser l'historique.
