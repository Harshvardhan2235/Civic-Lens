# ⚡ CivicLens

> **Civic infrastructure meets intelligent automation.** CivicLens is a full-stack, microservices-driven civic complaint management platform — built to bridge the gap between citizens and government through real-time issue reporting, ML-powered image classification, and a streamlined departmental resolution pipeline.

---

## 🏗️ Architecture Overview

CivicLens is composed of four independently deployable services, communicating over RESTful APIs with a shared MongoDB data layer and an isolated ML inference microservice.

```
civiclens/
├── backend-node/          # Express.js REST API — auth, complaints, votes, comments
├── backend-python/        # Flask ML microservice — ResNet50 image classification
├── citizen/               # Next.js citizen-facing portal
├── department/            # Next.js departmental dashboard
├── DATABASE_SETUP.md      # MongoDB provisioning guide
├── DEPLOYMENT.md          # Production deployment runbook
└── README.md
```

### System Topology

```
  [Citizen Portal]          [Department Portal]
      Next.js                    Next.js
         │                          │
         └──────────┬───────────────┘
                    ▼
           [Backend Node API]
           Express.js + JWT
                    │
          ┌─────────┴──────────┐
          ▼                    ▼
       MongoDB            [ML Service]
       (Atlas)           Flask + ResNet50
```

---

## 🧩 Services

### 🏙️ Citizen Portal — `citizen/`
**Next.js** — Server-side rendered, mobile-responsive citizen interface.

- **Auth flows** — JWT-based registration & login
- **Complaint submission** — Multi-step form with image upload pipeline routed through the ML classifier
- **Social layer** — Upvote/downvote system and threaded commenting
- **Status tracking** — Real-time complaint lifecycle visibility

---

### 🏛️ Department Portal — `department/`
**Next.js** — Jurisdiction-scoped dashboard for government departments.

- **Scoped complaint feed** — Filtered by department jurisdiction
- **Resolution workflow** — Status updates and resolution logging
- **Communication layer** — Comment interface for citizen-department dialogue

---

### ⚙️ Backend API — `backend-node/`
**Node.js / Express** — The core API layer. Handles all business logic, authentication, and service orchestration.

- **Auth** — JWT-secured endpoints for citizens and departments
- **Complaint engine** — Full CRUD with status lifecycle management
- **Voting system** — Idempotent upvote/downvote with aggregation
- **ML bridge** — Proxies image payloads to the Python inference service and persists classification results

---

### 🤖 ML Service — `backend-python/`
**Python / Flask + ResNet50** — A purpose-built inference microservice decoupled from the core API.

- **Model** — Transfer-learned ResNet50 (ImageNet backbone) fine-tuned for civic complaint image categorization
- **Endpoints** — RESTful prediction API consumed by the Node backend
- **Isolation** — Independent deployment lifecycle; can be scaled or swapped without touching the main API

---

## ⚙️ Prerequisites

| Dependency | Version |
|---|---|
| Node.js | ≥ 18.x |
| Python | ≥ 3.8 |
| MongoDB | Local or Atlas |
| npm / yarn | Latest stable |
| pip | Latest stable |

---

## 🚀 Local Development Setup

### 1. Database

Follow [DATABASE_SETUP.md](DATABASE_SETUP.md) to spin up and configure your MongoDB instance.

---

### 2. Backend API

```bash
cd backend-node
npm install
cp .env.example .env
# Configure DB URI, JWT secret, ML service URL
npm run dev
```

---

### 3. ML Inference Service

```bash
cd backend-python
pip install -r requirements.txt
cp .env.example .env
# Configure model path, port, allowed origins
python ml/predict.py
```

---

### 4. Citizen Portal

```bash
cd citizen
npm install
cp .env.example .env.local
# Set NEXT_PUBLIC_API_URL to your backend
npm run dev
```

---

### 5. Department Portal

```bash
cd department
npm install
cp .env.example .env.local
# Set NEXT_PUBLIC_API_URL to your backend
npm run dev
```

---

## 🔐 Environment Configuration

Each service is independently configured via its own `.env` / `.env.local` file. Reference the `.env.example` in each service directory for the complete variable manifest. Never commit secrets — use a secrets manager in production.

---

## ☁️ Deployment

Full deployment runbook in [DEPLOYMENT.md](DEPLOYMENT.md).

### Frontends → Vercel

| Service | Deploy Root |
|---|---|
| Citizen Portal | `citizen/` |
| Department Portal | `department/` |

### Backends → Render

| Service | Deploy Root |
|---|---|
| Node API | `backend-node/` |
| ML Service | `backend-python/` |

> Set all environment variables via your platform's secret management (Vercel env vars / Render environment groups) — never bake secrets into the build.

---

## 📡 API Surface

The Node.js backend exposes a structured RESTful API. Full endpoint documentation lives in `backend-node/README.md`.

**Domain areas:**
- `/auth` — Citizen & department authentication (register, login, token refresh)
- `/complaints` — Full CRUD, status transitions, ML classification metadata
- `/votes` — Upvote / downvote with deduplication
- `/comments` — Threaded comment system on complaints

---

## 🛠️ Tech Stack

| Layer | Tech |
|---|---|
| Citizen & Dept UI | Next.js, React |
| API Server | Node.js, Express, JWT |
| ML Inference | Python, Flask, ResNet50, PyTorch/TF |
| Database | MongoDB (Mongoose ODM) |
| Deployment | Vercel (frontend), Render (backend) |

---

> Built for cities that move fast. Complaints in — resolutions out.
