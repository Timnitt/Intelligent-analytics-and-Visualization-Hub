# Project Snapshot - Team Baseline

> **Purpose of this document:** This is an honest record of the state of the
> **Intelligent Analytics & Visualization Hub** at the moment it was published to this
> public repository. Everything described below was designed and built **as a team of six**
> during the **PowerCoders** program for **Elio Tax**. The original private repository's
> commit history was not carried over, so this document (together with the
> [project wiki](https://github.com/Timnitt/Intelligent-analytics-and-Visualization-Hub/wiki))
> serves as the permanent record of what the team built.
> All development after this snapshot is a continuation on top of the team's work.

- **Snapshot date:** July 2026
- **Baseline commit:** `10dbc75` (public repo)
- **Original project:** PowerCoders Bootcamp - `project-intelligent-analytics-and-visualization-hub` (private)

---

## 👥 Team & Credit

This project was developed by a team of six. Roles and ownership as documented in the project wiki:

| Member | Roles | Primary ownership |
|---|---|---|
| **Timnit** | Product Owner, Co-Architect Full-Stack Developer | Product vision, system architecture, UAT and secure link sharing |
| **Gabryela** | Scrum Master, Developer | CI/CD pipeline, sprint ceremonies, security model spec |
| **Burcu** | Frontend Developer | `frontend/` — React UI, chart components |
| **Recep** | Backend Developer, AI Engineer | `backend/` — API, database, RBAC, query building |
| **Aleksei** | Co-Architect, Full-Stack Developer | `shared/` types, AI adapters and engines |
| **Heba** | Co-Product Owner, Co-Architect, QA Lead | Testing, feature validation, static dashboard content |


---

## 📝 What the product is

A web application that lets Elio Tax analysts explore Canadian tax/revenue data
**conversationally**. A user asks a plain-English question (e.g. *"show me revenue by
province for 2023"*); an AI layer translates it into a structured chart configuration,
the backend builds and runs the SQL, and the frontend renders the right visualization —
no SQL knowledge required.

---

## 🛠️ Tech stack (as built)

| Layer | Technology |
|---|---|
| Language | TypeScript (frontend + backend + shared types) |
| Backend | Node.js, Express 4, Apollo Server 5 |
| GraphQL schema | Auto-generated from Sequelize models via `graphql-gene` |
| Database | SQLite via Sequelize ORM |
| AI engine | Google Gemini (`@google/generative-ai`) with a rule-based LocalEngine fallback |
| Frontend | React 19, Vite, Tailwind CSS 4, React Router 7 |
| Charts | Chart.js 4 (`react-chartjs-2`) + treemap & matrix plugins, `chartjs-plugin-datalabels` |
| Maps | `react-simple-maps` with Canadian provinces GeoJSON |
| Auth | JWT (HS256) + bcrypt password hashing, role-based access control |
| Testing | Jest + ts-jest + supertest (backend), separate QA fixture harness at repo root |
| CI | GitHub Actions (`.github/workflows/ci.yml`) |

---

## ✨ Implemented features (verified in code at this snapshot)

### 🔐 Authentication & authorization
- JWT login (`POST /api/auth/login`) with bcrypt-hashed passwords
- Three-tier RBAC: **viewer → analyst → admin**, enforced server-side by middleware
- Tokens stored in `sessionStorage` (cleared when the tab/browser closes)
- Admin user management endpoints (`/api/admin/users`)
- Security posture per the team's spec: identical error messages for wrong email vs.
  wrong password (prevents user enumeration); frontend role checks treated as UX only —
  enforcement is exclusively backend

### 🤖 AI query pipeline (the core innovation: "deterministic AI")
The team's defining design decision: **AI output is advisory, never authoritative.**
- `POST /api/ai/query` (analyst/admin only) accepts a natural-language question
- **AIAdapter** (`backend/src/ai/adapter.ts`): SHA-1 cache lookup → primary engine with a
  3-second timeout → automatic fallback to LocalEngine on any failure
- **GeminiEngine** — prompt-engineered calls to the Gemini API
- **LocalEngine** — regex/keyword rule engine, fully offline, used as silent fallback
  (also the default when no `GEMINI_API_KEY` is configured)
- **Normalizer** (`backend/src/ai/normalizer.ts`) — deterministically re-derives chart
  type, metric, and status filters from the literal question text and **overrides the AI**
  when they disagree, guaranteeing reproducible results
- **Guard checks** — friendly rejections for impossible requests: non-geographic data on
  maps, dual metrics on single-value charts (pie/donut/treemap/heatmap/map),
  out-of-scope questions — each with a suggested alternative query
- **SQL query builder** (`backend/src/sql/queryBuilder.ts`) — pure functions producing
  parameterized SQL, including a two-step rank + time-series path for "top N" questions
- **AI insights** (`backend/src/ai/insights.ts`) — generated commentary below charts,
  cached in memory per question + data signature
- Response reports which engine answered (Gemini or Local) and whether it was cached

### 📊 Analytics dashboard
- KPI cards: Gross Revenue, Net Subtotal, Total Tax Collected
- Interactive Canada map (orders by province) with a Maritimes zoom inset and
  capital-city markers
- Static charts: Yearly Revenue (line), Orders by Status (bar), Top Product Groups,
  Top Provinces, Category Revenue, Lowest-Performing Products
- Filter bar (year range, province, status, category) with session persistence
- 9 chart types supported end-to-end: bar, line, pie, doughnut, treemap, heatmap,
  map, stat, grid

### 🔗 Secure dashboard sharing
- Analyst/admin captures the current filter state into a UUIDv4 share link
  (`/api/dashboard-shares`, stored in SQLite)
- Viewer must be authenticated; backend enforces role — viewers receive a read-only,
  non-interactive version (filter badges instead of dropdowns, no share button)
- Shared view isolated from the main layout; invalid/expired links show a friendly error
- Separate chart-level share routes (`/api/charts`, `/api/shared-charts`)

### 🧪 Testing
- Backend Jest suites: adapter, normalizer, query builders, auth login, RBAC middleware,
  admin user routes, chart routes, GraphQL
- Root-level QA harness (`fixtures/seed.js`, `fixtures/verify.js`) plus a written
  `TEST_PLAN.md`
- GitHub Actions CI workflow

---

## 📚 Documentation the team produced

- **README** — setup, features, architecture guardrails
- **12-page project wiki**: Overview, HLD & LLD architecture, API endpoints with a
  role-permission matrix, the governed JSON Contract, monorepo structure & ownership,
  team roles & decision authority, original + revised sprint plans, AI pipeline deep
  dive, static dashboard metric definitions, and a security model specification
- **TEST_PLAN.md** — QA test plan

---

## Known gaps at this snapshot

Recorded here so the baseline is truthful, not idealized:

- **Doc/code drift:** some wiki pages describe earlier designs — endpoint names differ
  (`/api/share` in the wiki vs. `/api/dashboard-shares` in code; `/api/users` vs.
  `/api/admin/users`), the LLD mentions a `Towns` table and a migrations folder that
  don't exist (tables are created directly in `server.ts`), and the Overview lists 5
  chart types while 9 are implemented.
- **Caches are unbounded** in-memory `Map`s (AI adapter + insights); the wiki's
  planned 10-minute TTL was not implemented.
- **No rate limiting** on login, despite being listed in the security checklist.
- **`backend/database.sqlite` is committed** to the repository (seeded demo data).
- **Share links never expire** (by design per the LLD, but worth knowing).
- Sprint 4's revised plan left three items unassigned at the time (query caching with
  auto-refresh, chart sharing via link, shared-link security) — sharing was
  subsequently implemented; caching remains the simple in-memory version.

---

