# SRC Campus Portal

> A secure, centralised web platform for student democracy and campus life — combining SRC elections, society management, and campus events into one official student hub.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Environment Variables](#environment-variables)
- [Database Setup](#database-setup)
- [Running the App](#running-the-app)
- [API Documentation](#api-documentation)
- [Testing](#testing)
- [Deployment](#deployment)
- [Team](#team)
- [Course Information](#course-information)

---

## Overview

The SRC Campus Portal solves three real problems at our university:

- **SRC elections** are currently run via paper ballots or Google Forms — both insecure and inaccessible to off-campus students
- **Society information** is scattered across WhatsApp groups and physical notice boards
- **Event announcements** get lost in mass emails with no central feed

This platform gives students one trusted place to vote, follow societies, and RSVP to events — all managed by the SRC from a single admin dashboard.

### Key Technical Highlight — Ballot Anonymity

The voting module implements a genuine anonymity architecture: the system proves a student voted *without* recording *what* they voted for. The `voter_registry` and `ballots` tables are permanently separated with no foreign key link between them — meaning no query can ever trace a ballot back to a specific student.

---

## Features

### Voting Module
- Secure login via university email + OTP verification
- Browse candidate profiles, bios, and manifestos
- Cast anonymous ballots per position
- One-vote-per-position enforcement with database transactions
- Live voter turnout dashboard for admin
- Results published only after election closes

### Societies & Events Module
- Browse and follow student societies
- Personalised events feed based on followed societies
- RSVP to events with capacity enforcement
- Society leaders manage their own pages and post events
- Push/email notifications for new events

### Admin Dashboard
- Create and configure elections (positions, dates, eligibility)
- Approve or reject candidate registrations
- Approve new society registrations
- View real-time entry logs and generate reports
- Manage all user roles from one panel

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React.js + Tailwind CSS |
| Backend | Node.js + Express |
| Database | PostgreSQL |
| Authentication | JWT + bcrypt + Email OTP |
| Real-time | Socket.io |
| File Storage | Cloudinary |
| Email | Nodemailer + Gmail |
| Deployment | Render.com |

---

## Getting Started

### Prerequisites

Make sure you have the following installed:

- [Node.js](https://nodejs.org/) v18 or higher
- [PostgreSQL](https://www.postgresql.org/) v14 or higher
- [Git](https://git-scm.com/)
- A Gmail account (for OTP emails)
- A free [Cloudinary](https://cloudinary.com/) account (for image uploads)

### Installation

1. **Clone the repository**

```bash
git clone https://github.com/your-username/src-campus-portal.git
cd src-campus-portal
```

2. **Install server dependencies**

```bash
cd server
npm install
```

3. **Install client dependencies**

```bash
cd ../client
npm install
```

4. **Set up environment variables** (see [Environment Variables](#environment-variables))

5. **Set up the database** (see [Database Setup](#database-setup))

6. **Run the application** (see [Running the App](#running-the-app))

---

## Project Structure

```
src-campus-portal/
├── client/                   # React frontend
│   ├── public/
│   └── src/
│       ├── components/       # Reusable UI components
│       ├── pages/            # Route-level page components
│       │   ├── student/      # Student-facing pages
│       │   ├── admin/        # Admin dashboard pages
│       │   └── auth/         # Login and OTP pages
│       ├── hooks/            # Custom React hooks
│       ├── api/              # API call functions
│       └── utils/            # Helper functions
│
├── server/                   # Node.js + Express backend
│   ├── routes/               # API route definitions
│   ├── controllers/          # Request handlers / business logic
│   ├── models/               # Database query functions
│   ├── middleware/            # Auth checks, error handling
│   └── utils/                # Email, OTP, QR code helpers
│
├── database/
│   ├── schema.sql            # Table definitions
│   └── seed.sql              # Demo data for development
│
├── docs/                     # Project documentation
│   ├── architecture.png      # System architecture diagram
│   ├── erd.png               # Entity Relationship Diagram
│   ├── wireframes/           # UI wireframes
│   └── api.md                # Full API reference
│
└── README.md
```

---

## Environment Variables

Create a `.env` file inside the `server/` directory. **Never commit this file to GitHub.**

```env
# Database
DATABASE_URL=postgresql://your_user:your_password@localhost:5432/src_portal

# Authentication
JWT_SECRET=replace-with-a-long-random-string

# Email (OTP)
EMAIL_USER=your-gmail@gmail.com
EMAIL_PASS=your-gmail-app-password

# Cloudinary (image uploads)
CLOUDINARY_CLOUD_NAME=your-cloud-name
CLOUDINARY_API_KEY=your-api-key
CLOUDINARY_API_SECRET=your-api-secret

# Server
PORT=5000
NODE_ENV=development
```

> **Getting a Gmail App Password:** Go to your Google Account → Security → 2-Step Verification → App Passwords. Generate one for "Mail".

---

## Database Setup

1. **Create the database**

```bash
psql -U postgres
CREATE DATABASE src_portal;
\q
```

2. **Run the schema** (creates all tables)

```bash
psql -U postgres -d src_portal -f database/schema.sql
```

3. **Seed demo data** (optional but recommended for development)

```bash
psql -U postgres -d src_portal -f database/seed.sql
```

The seed script creates:
- 15 demo student accounts
- 1 active SRC election with 3 positions
- 4 candidates per position with bios and manifestos
- 3 student societies
- 4 upcoming events

### Core Tables

| Table | Purpose |
|---|---|
| `users` | All system users (students, admins, guards) |
| `elections` | Election records with start/end dates and status |
| `positions` | Positions within each election (e.g. President) |
| `candidates` | Candidate registrations linked to positions |
| `voter_registry` | Records **who** has voted — never linked to ballots |
| `ballots` | Records **what** was voted — never linked to voters |
| `societies` | Student society records |
| `society_members` | Society follows/memberships |
| `events` | Campus events posted by society leaders |
| `rsvps` | Event RSVPs per student |
| `entry_logs` | Campus access records (guard verification) |

---

## Running the App

### Development (with hot reload)

Open two terminals:

**Terminal 1 — Backend:**
```bash
cd server
npm run dev
```
Server runs at `http://localhost:5000`

**Terminal 2 — Frontend:**
```bash
cd client
npm start
```
App runs at `http://localhost:3000`

### Production build

```bash
cd client
npm run build
```

---

## API Documentation

Full API reference is in [`docs/api.md`](docs/api.md). Key endpoints:

### Authentication
| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/auth/login` | Send OTP to university email |
| POST | `/api/auth/verify-otp` | Verify OTP and receive JWT |
| POST | `/api/auth/logout` | Invalidate session |

### Elections
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/elections` | Student | List all elections |
| GET | `/api/elections/:id/candidates` | Student | Get candidates for an election |
| POST | `/api/elections/:id/vote` | Student | Submit anonymous ballot |
| GET | `/api/elections/:id/results` | Student | Get results (closed elections only) |
| POST | `/api/admin/elections` | Admin | Create new election |
| PATCH | `/api/admin/elections/:id/status` | Admin | Open or close an election |

### Societies & Events
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/societies` | Student | Browse all societies |
| POST | `/api/societies/:id/follow` | Student | Follow a society |
| GET | `/api/events` | Student | Get personalised events feed |
| POST | `/api/events/:id/rsvp` | Student | RSVP to an event |
| POST | `/api/admin/societies` | Admin | Approve a society registration |

All protected routes require a `Bearer` token in the `Authorization` header.

---

## Testing

### Running tests

```bash
cd server
npm test
```

### Test coverage areas

- **Unit tests** — ballot anonymity logic, one-vote enforcement, OTP expiry, election status validation
- **Integration tests** — full voting flow, society follow + RSVP flow, admin election management
- **Security tests** — unauthorised access attempts, expired JWT rejection, double-vote race conditions

### Test accounts (after seeding)

| Role | Email | OTP |
|---|---|---|
| Student | student@university.ac.sz | `123456` |
| Admin | admin@university.ac.sz | `123456` |
| Society Leader | leader@university.ac.sz | `123456` |

> OTPs are hardcoded in development mode only. In production, real OTPs are emailed.

---

## Deployment

The app is deployed on [Render.com](https://render.com) using free tier services.

- **Backend API:** `https://src-portal-api.onrender.com`
- **Frontend:** `https://src-portal.onrender.com`

### Deploy your own instance

1. Push your code to GitHub
2. Create a new **Web Service** on Render, connect your GitHub repo, set root to `server/`
3. Add all environment variables from `.env` into Render's environment settings
4. Create a **PostgreSQL** database on Render and update `DATABASE_URL`
5. Create a new **Static Site** on Render, connect the same repo, set root to `client/`, build command `npm run build`, publish directory `build/`
6. Run the schema and seed scripts against the production database

---

## Team

| Name | Role | Responsibilities |
|---|---|---|
| Person 1 | Frontend Lead | Student UI, voting flow, events feed, mobile responsiveness |
| Person 2 | Backend Lead | REST API, ballot anonymity, business logic |
| Person 3 | Auth & Notifications | OTP login, JWT, email notifications |
| Person 4 | Admin Dashboard | Election management, society approvals, analytics |
| Person 5 | QA & DevOps | Testing, deployment, documentation |

---

## Course Information

**Course:** Practices in Software Engineering  
**Institution:** University of Eswatini  
**Year:** 2026  
**Supervisor:** [Supervisor Name]

### Academic Links
- [Project Proposal](docs/proposal.docx)
- [Requirements Specification](docs/requirements.md)
- [System Design Document](docs/design.md)
- [Test Report](docs/test-report.md)
- [Final Report](docs/final-report.docx)

---

> Built as part of a Software Engineering course project. The SRC Campus Portal is designed to be practically adoptable by any university running SRC elections and managing student societies.
