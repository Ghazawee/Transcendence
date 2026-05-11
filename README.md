# 🕹️ ft_transcendence

![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Fastify](https://img.shields.io/badge/Fastify-000000?style=for-the-badge&logo=fastify&logoColor=white)
![Prisma](https://img.shields.io/badge/Prisma-2D3748?style=for-the-badge&logo=prisma&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)
![42 School](https://img.shields.io/badge/42-ft__transcendence-000000?style=for-the-badge)

A full-stack Pong application built with a microservice-style backend, secure authentication, and containerized deployment.

> The project combines gameplay logic, account management, social features, and production-style service orchestration behind an HTTPS reverse proxy.

---

## 📚 Table of Contents

- [✨ Highlights](#-highlights)
- [🏗️ Architecture](#️-architecture)
- [🧰 Tech Stack](#-tech-stack)
- [📁 Project Structure](#-project-structure)
- [🚀 Getting Started](#-getting-started)
- [🔌 Service Endpoints (through Nginx)](#-service-endpoints-through-nginx)
- [🧪 Core Functional Coverage](#-core-functional-coverage)
- [🎯 Gameplay Features](#-gameplay-features)
- [🔐 Security Notes](#-security-notes)
- [📌 Notes](#-notes)
- [👥 Team Project](#-team-project)

---

## ✨ Highlights

- 🔐 **Authentication service** with:
  - Email/password registration and login
  - Google OAuth login
  - JWT access and refresh token flow
  - TOTP-based 2FA (QR setup and verification)
- 👤 **User service** with profile management and avatar upload support
- 🤝 **Friends system** (send, accept, and reject friend requests)
- 🎮 **Game service** for match history and statistics
- 🧩 **Modular backend services** (auth/user/game + gateway scaffolding)
- 🧠 Multiple game modes in frontend logic:
  - Local multiplayer (same machine)
  - Local PvP
  - PvAI
  - Tournament (including 4 and 8 player brackets)
  - Four-player mode components
- 🌍 i18n support (`en`, `fr`, and `ar`)
- 🔒 Nginx reverse proxy with TLS termination
- 🐳 Fully containerized with Docker Compose

---

## 🏗️ Architecture

The platform is organized into independent services:

- **nginx** → single entrypoint over HTTPS
- **frontend** → Vite + TypeScript SPA
- **auth** → auth, tokens, Google, 2FA
- **user** → profiles, avatars, friends
- **game** → match recording and stats
- **ws-gateway** → present in the repository as gateway scaffolding (not part of the delivered local multiplayer flow)
- **sqlite volume** shared through Prisma models

High-level request flow:

1. Client connects to `https://localhost:8443`
2. Nginx routes `/auth`, `/user`, and `/game` (`/ws` is available but not used in current gameplay)
3. Backend services communicate over the internal Docker network
4. Prisma persists data into SQLite

---

## 🧰 Tech Stack

### Languages
- TypeScript
- JavaScript
- HTML / CSS
- SQL (SQLite via Prisma)
- Shell (Makefile + scripts)

### Backend
- Node.js
- Fastify
- Prisma ORM
- JWT authentication
- Google OAuth (`google-auth-library`)
- TOTP 2FA (`otpauth`, `qrcode`)
- Axios for service-to-service calls

### Frontend
- Vite
- TypeScript SPA architecture
- Tailwind CSS + PostCSS
- Custom game rendering/engine modules

### Infrastructure / DevOps
- Docker & Docker Compose
- Nginx (reverse proxy + SSL)
- Self-signed certificate generation script
- Makefile workflow for local orchestration

---

## 📁 Project Structure

```text
.
├── Makefile
├── srcs/
│   ├── docker-compose.yml
│   ├── backend/
│   │   ├── auth/
│   │   ├── user/
│   │   ├── game/
│   │   ├── ws-gateway/
│   │   └── prisma/
│   ├── frontend/
│   └── nginx/
└── README.md
```

Backend domains:

- **Auth domain**: register, login, session, token refresh, Google auth, password change, account deletion, 2FA
- **User domain**: profile retrieval/update, avatar upload handling, friend requests and responses
- **Game domain**: match persistence, history retrieval, aggregate stats (wins/losses/win rate)

---

## 🚀 Getting Started

### 1) Prerequisites

- Docker
- Docker Compose
- Make (optional but recommended)

### 2) Configure environment

Create a `.env` file at the repository root.

Minimum variables used by services:

- `JWT_ACCESS_SECRET`
- `JWT_REFRESH_SECRET`
- `GOOGLE_CLIENT_ID`
- `GOOGLE_CLIENT_SECRET`
- `VITE_GOOGLE_CLIENT_ID`
- `VITE_API_BASE_URL`

> Tip: use strong random secrets for JWT variables.

Optional variables may exist depending on how services are launched locally.

Recommended: Keep `.env` out of version control and rotate secrets before sharing or deploying.

### 3) Run the platform

Using Make:

- `make up` → build and start all services
- `make down` → stop all services
- `make logs` → stream logs
- `make restart` → restart services
- `make clean` / `make fclean` → cleanup containers, volumes, certs

Direct Docker Compose:

- `cd srcs && docker compose --env-file ../.env up --build -d`

### 4) Open the app

- **Frontend**: `https://localhost:8443`

Since certificates are self-signed locally, your browser may show a warning at first launch.

You can verify service status with `make status`.

---

## 🔌 Service Endpoints (through Nginx)

- `/auth/*` → authentication APIs
- `/user/*` → profile + friends APIs
- `/game/*` → matches + stats APIs
- `/public/*` → static assets (avatars/uploads)

> Note: Gameplay in this version is local multiplayer on the same machine. Online matchmaking and remote real-time multiplayer are not part of the delivered scope.

Health checks:

- `/auth/health`
- `/user/health`
- `/game/health`

---

## 🧪 Core Functional Coverage

- Account registration and login
- Session validation (`/auth/me` pattern)
- Token refresh and logout
- Google sign-in flow
- 2FA setup, verify, disable, and login validation
- User profile updates and avatar uploads
- Friends request/response workflow
- Match recording + historical stats
- Local game sessions with persisted results

---

## 🎯 Gameplay Features

- Keyboard-based Pong gameplay experience
- AI opponent mode
- Local PvP mode
- Tournament progression logic (4 and 8 players)
- Match outcome persistence to backend stats
- Player-facing statistics page from recorded matches

---

## 🔐 Security Notes

- HTTPS entrypoint via Nginx
- JWT-based auth for protected routes
- Optional 2FA for stronger account security
- Server-side validation and protected service routes

For production deployment, replace self-signed certs with trusted CA certificates and harden environment/secret management.

---

## 📌 Notes

- This repository is organized for a local containerized environment.
- The frontend uses a custom SPA router and game modules (not a React/Vue framework).
- Prisma schema defines `User`, `Friend`, and `Match` domain models.
- The repository includes a `ws-gateway` service scaffold, while the delivered gameplay flow focuses on local multiplayer.

---

## 👥 Team Project

Built as a collaborative full-stack project in the spirit of **42 ft_transcendence**: game programming, modern web technologies, and service-oriented architecture.
