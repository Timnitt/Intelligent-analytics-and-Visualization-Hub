<div align="center">

# 📊 Elio Tax Analytics and Visualisation Hub

---

**The AI-Powered Analytics Platform for Elio Tax Leadership and Analysts**

![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-18+-339933?logo=nodedotjs&logoColor=white)
![React](https://img.shields.io/badge/React-Vite-61DAFB?logo=react&logoColor=black)
![GraphQL](https://img.shields.io/badge/GraphQL-graphql--gene-E10098?logo=graphql&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-Sequelize-003B57?logo=sqlite&logoColor=white)
![Chart.js](https://img.shields.io/badge/Chart.js-react--chartjs--2-FF6384?logo=chartdotjs&logoColor=white)
![Gemini](https://img.shields.io/badge/AI-Gemini_API-8E75B2?logo=googlegemini&logoColor=white)
![License](https://img.shields.io/badge/License-Case_Study-A62639)

</div>

> An intelligent analytics sandbox dashboard built for **Elio Tax** during the [**PowerCoders**](https://powercoders.org/) program - explore, visualise, and share operational and commercial data without writing a single line of SQL.

<div align="center">

[Overview](#-project-overview) · [Tech Stack](#️-tech-stack--architecture) · [Features](#-features) · [Guardrails](#-core-architectural-guardrails) · [Project Structure](#-project-structure) · [Setup](#️-setup--configuration) · [Team](#-team)

</div>

---

## 📝 Project Overview

Leadership lacks a single, flexible place to explore operational and commercial signals (such as tax or revenue data). With this solution, a user can explore, visualise, and share operational and commercial data - without writing a single line of SQL. A user can ask a business question (e.g., *"revenue by category"*) to AI. An AI-infused layer then interprets the required filters, aggregates, picks an appropriate visual representation, and renders the result.

---

## 🛠️ Tech Stack & Architecture

| Layer | Technology |
|---|---|
| Language | TypeScript |
| Runtime | Node.js |
| Backend API | GraphQL (`graphql-gene`) |
| Database | SQLite (via Sequelize ORM) |
| Frontend | React + Vite |
| Charts | Chart.js (`react-chartjs-2`) |
| Auth | JWT (Role-Based Access Control) |
| AI Engine | Gemini API + Local fallback engine |

---

## ✨ Features

### 🔐 Authentication
- JWT-based login with session storage (clears on tab/browser close)
- Role decoded from token on every session start
- Logout clears the session immediately across all pages

### 📊 Analytics Dashboard
- KPI cards: Gross Revenue, Net Subtotal, Total Tax Collected
- Interactive Canada map (orders by province with Maritimes inset)
- Charts: Yearly Revenue (line), Orders by Status (bar), Top Product Groups, Top Provinces, Category Revenue, Lowest-Performing Products
- Filter bar: year range, province, status, category
- Filters persist within the session

### 🤖 AI Assistant (Analyst/Admin only)
- Natural language query input
- Supports: bar, line, pie, doughnut, treemap, heatmap, map, stat, grid chart types
- Shows engine used (Gemini AI or Local) and response latency
- AI insights panel below charts

### 🔗 Secure Dashboard Sharing
- Analyst/Admin creates a share link capturing the current filter state
- Share link is a UUID stored in SQLite (`SharedDashboards` table)
- The viewer must be authenticated to open a share link
- Backend enforces role: viewers receive `interactive: false`
- Shared view is read-only: filter badges instead of dropdowns, no Share button
- Isolated from main layout - no sidebar, no nav links
- Invalid/expired links show a user-friendly error

---

## 🔒 Core Architectural Guardrails

- **AI Engine Isolation:** The AI assistant is decoupled from the GraphQL and visualisation core - engines can be swapped without major refactor.
- **JSON Contract Alignment:** Backend adapter and AI prompt output are bound by a strict data contract to prevent integration friction.
- **Role-Based Access Control:** Share URLs use secure UUID identifiers whose effective view depends on the authenticated user's role - no sensitive data leaks through naive links.
- **Session Security:** All tokens stored in `sessionStorage` - automatically cleared when the browser tab is closed.

---

## 📁 Project Structure

```text
├── backend/
│   ├── models.ts                          # Sequelize models (User, SharedDashboard, etc.)
│   ├── server.ts                          # Express + Apollo server setup
│   ├── seed.ts                            # Seed script for test users
│   └── src/
│       ├── auth/                          # JWT middleware, RBAC
│       ├── graphql/                       # Resolvers and schema
│       └── share/
│           └── dashboardShareRoutes.ts    # POST & GET /api/dashboard-shares
├── frontend/
│   └── src/
│       ├── App.tsx                        # Root: auth, routing, AI assistant
│       ├── components/
│       │   ├── AdminPanel.tsx             # User management
│       │   ├── CanadaMap.tsx              # Province map
│       │   ├── Dashboard.tsx              # Main analytics dashboard
│       │   ├── DashboardLayout.tsx        # Sidebar + top bar layout
│       │   ├── KpiCard.tsx                # Metric cards
│       │   └── SharedDashboardView.tsx    # Shared link viewer
│       └── hooks/
│           ├── useDashboardStats.ts       # GraphQL data fetching
│           └── useFilterOptions.ts        # Filter dropdown options
└── shared/
    └── types/
        └── share.ts                       # Shared TS types for dashboard sharing
```

---

## ⚙️ Setup & Configuration

### Prerequisites

Before getting started, ensure you have **Node.js 18+** and **npm** installed on your machine.

### Backend Environment Variables

Create a `.env` file in the `backend/` folder with the following:

```env
PORT=4000
JWT_SECRET=your_secret_key_here
GEMINI_API_KEY=your_gemini_api_key_here   # optional - falls back to local engine
```

### Running Locally

```bash
git clone https://github.com/Powercoders-Bootcamp/project-intelligent-analytics-and-visualization-hub
cd project-intelligent-analytics-and-visualization-hub

git fetch origin main
git checkout main
git pull origin main
```

**Backend**

```bash
cd backend

# Install dependencies
npm install

# Create and configure your .env file
cp .env.example .env
# Quick shortcut to set a dev secret (or open the file and edit manually)
echo "JWT_SECRET=devsecret123" > .env

# Seed the database with test data
npm run seed

# Start the backend server
npm run dev
```

**Frontend**

```bash
cd frontend

# Install dependencies
npm install --legacy-peer-deps

# Start the frontend development server
npm run dev
```

Open in browser: [http://localhost:5173](http://localhost:5173)

---

## 👥 Team

This project was developed by a team of 6 members:

- Timnit
- Gabryela
- Burcu
- Recep
- Aleksei
- Heba

See [PROJECT_SNAPSHOT.md](PROJECT_SNAPSHOT.md) for the full record of the team baseline.
