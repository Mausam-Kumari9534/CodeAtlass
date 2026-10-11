<div align="center">

# 🗺️ CodeAtlas (RepoSamjho)
### *Understand Any GitHub Repository in Minutes — in Simple English & Hinglish*

[![Live Frontend](https://img.shields.io/badge/Live%20Frontend-Cloudflare%20Pages-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://codeatlass.pages.dev)
[![Live Backend](https://img.shields.io/badge/Live%20Backend-Render-46E3B7?style=for-the-badge&logo=render&logoColor=white)](https://codeatlass-1.onrender.com)
[![Database](https://img.shields.io/badge/PostgreSQL-Neon.tech-00E599?style=for-the-badge&logo=postgresql&logoColor=black)](https://neon.tech)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev)

<br />

**CodeAtlas** (also known as *RepoSamjho*) is an intelligent, developer-friendly GitHub repository explainer. Just paste any public GitHub repository link and get instant interactive folder maps, architectural data flow visualizations, technology stack detection, and key file breakdowns explained in simple, beginner-friendly **English** or **Hinglish**.

[Explore Live Demo](https://codeatlass.pages.dev) · [Backend API Status](https://codeatlass-1.onrender.com/api/health) · [Report Bug](https://github.com/Mausam-Kumari9534/CodeAtlass/issues)

</div>

---

## 📑 Table of Contents

- [✨ Key Features](#-key-features)
- [🏗️ System Architecture](#️-system-architecture)
- [💻 Tech Stack Breakdown](#-tech-stack-breakdown)
- [📊 Database Schema](#-database-schema)
- [⚡ API Reference](#-api-reference)
- [🔐 Environment Variables](#-environment-variables)
- [🚀 Quick Start (Local Setup)](#-quick-start-local-setup)
- [☁️ Production Deployment](#️-production-deployment)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)

---

## ✨ Key Features

- **⚡ Instant Repository Parsing:** Automatically inspects GitHub file trees, package manifests, and project structure in seconds via GitHub REST API.
- **🗣️ Bilingual Explanations (English & Hinglish):** Switch seamlessly between standard technical English and conversational Hinglish so developers of all skill levels can easily grasp complex codebases.
- **🧩 Automatic Tech Stack & Framework Detection:** Identifies Next.js, React, Vue, Express, Django, FastAPI, Go, Rust, Tailwind CSS, Drizzle, Prisma, Docker, and more from file signatures and dependency manifests.
- **🗺️ Interactive Architecture & Data Flow Visualizer:** Step-by-step interactive diagram walking through how requests travel through the repository from UI to API to Database.
- **📁 AST & Smart Folder Catalog:** Explains the specific responsibility of every directory (e.g., `controllers/`, `routes/`, `lib/`, `middlewares/`).
- **📑 Multi-Format Export:** Export complete analysis reports into nicely formatted **Markdown (`.md`)** files or print to **PDF**.
- **⚡ Two-Tier Caching (Redis + PostgreSQL):**
  - **Redis Layer:** 2-hour TTL cache for instant sub-second response on repeated searches.
  - **PostgreSQL Layer:** Persistent history storing analyzed repositories for quick access on the homepage.

---

## 🏗️ System Architecture

```mermaid
flowchart TD
    subgraph Client["Frontend Client (Cloudflare Pages)"]
        UI["React 18 + Vite SPA"]
        Landing["Landing Page & Recent Repos"]
        Visualizer["Architecture & Data Flow Viewer"]
        Translator["Bilingual Switcher (EN / HI)"]
    end

    subgraph CDN["Cloudflare Edge"]
        CF["Cloudflare Global Network"]
    end

    subgraph Server["Backend Server (Render Web Service)"]
        Express["Express.js (TypeScript)"]
        Analyzer["Heuristic AST & Tech Analyzer"]
        Knowledge["Knowledge Base (100+ File Rules)"]
        GHClient["GitHub REST API Client"]
    end

    subgraph Data["Data & Caching Layer"]
        Postgres[("Neon.tech Serverless PostgreSQL\n(Drizzle ORM)")]
        Redis[("Upstash Redis\n(2h Cache TTL)")]
    end

    subgraph External["External APIs"]
        GitHubAPI["GitHub API (github.com)"]
    end

    UI --> CF
    UI -- "POST /api/analyze\nGET /api/recent" --> Express
    Express --> Redis
    Redis -- "Cache Miss" --> GHClient
    GHClient --> GitHubAPI
    Express --> Analyzer
    Analyzer --> Knowledge
    Analyzer --> Postgres
    Analyzer --> Redis
```

---

## 💻 Tech Stack Breakdown

### Frontend (`/frontend`)
| Technology | Version | Purpose |
| :--- | :--- | :--- |
| **React** | `^18.3.1` | Component-based interactive UI library |
| **Vite** | `^5.4.2` | Ultra-fast build tool and local dev server |
| **TypeScript** | `^5.5.3` | Type safety and autocompletion |
| **Tailwind CSS** | `^3.4.1` | Utility-first styling with modern dark/light grid aesthetics |
| **Framer Motion** | `^11.0.8` | Fluid UI transitions, spring animations, and tab reveals |
| **Lucide React** | `^0.344.0` | Clean, modern SVG icon library |

### Backend (`/backend`)
| Technology | Version | Purpose |
| :--- | :--- | :--- |
| **Node.js** | `>=18.x` | Runtime environment (Node v24 on Render) |
| **Express.js** | `^4.18.2` | RESTful API server handling analyze & recent requests |
| **Drizzle ORM** | `^0.30.4` | TypeScript-first, lightweight SQL ORM |
| **PostgreSQL (pg)** | `^8.11.3` | PostgreSQL connection pool driver |
| **Redis** | `^4.6.13` | High-speed cache client with auto-reconnect & fallback |
| **dotenv** | `^18.0.5` | Environment variable management |
| **tsx** | `^4.7.1` | TypeScript execution and hot-reloading for local development |

### Cloud Infrastructure
| Component | Provider | Tier |
| :--- | :--- | :--- |
| **Frontend Hosting** | Cloudflare Pages | Free Tier (Global Edge Network) |
| **Backend Web Service** | Render | Free Web Service (Auto-Deploy on Git Push) |
| **Database** | Neon.tech | Free Serverless PostgreSQL |
| **Cache Storage** | Upstash | Free Serverless Redis |

---

## 📊 Database Schema

CodeAtlas uses PostgreSQL managed through **Drizzle ORM**.

### Table: `analyses`
Stores full repository analysis snapshots and power the *"Recently Analyzed"* section on the homepage.

```sql
CREATE TABLE IF NOT EXISTS analyses (
  id SERIAL PRIMARY KEY,
  owner TEXT NOT NULL,
  repo TEXT NOT NULL,
  full_name TEXT NOT NULL,
  description TEXT,
  stars INTEGER DEFAULT 0 NOT NULL,
  language TEXT,
  tree JSONB NOT NULL,
  meta JSONB NOT NULL,
  analysis JSONB NOT NULL,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

| Column Name | Data Type | Nullable | Description |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | No | Primary Key auto-incrementing ID |
| `owner` | `TEXT` | No | GitHub username or organisation (e.g. `facebook`) |
| `repo` | `TEXT` | No | Repository name (e.g. `react`) |
| `full_name` | `TEXT` | No | Full slug (`owner/repo`) |
| `description` | `TEXT` | Yes | GitHub repository description |
| `stars` | `INTEGER` | No | GitHub star count at time of scan |
| `language` | `TEXT` | Yes | Primary programming language |
| `tree` | `JSONB` | No | Structured hierarchical JSON file tree |
| `meta` | `JSONB` | No | Repository metadata (forks, default branch, watchers) |
| `analysis` | `JSONB` | No | Generated summary, tech stack, data flows, and file purposes |
| `created_at` | `TIMESTAMP` | No | Analysis timestamp (`NOW()`) |

---

## ⚡ API Reference

Base Production URL: `https://codeatlass-1.onrender.com`

### 1. Health Check
Checks backend health and server availability.
- **Route:** `GET /api/health`
- **Response:**
  ```json
  {
    "ok": true
  }
  ```

### 2. Fetch Recently Analyzed Repos
Returns the last unique repositories analyzed.
- **Route:** `GET /api/recent`
- **Response:**
  ```json
  {
    "recent": [
      {
        "id": 1,
        "fullName": "Mausam-Kumari9534/CodeAtlass",
        "description": "Smart GitHub Repository Explainer",
        "stars": 12,
        "language": "TypeScript"
      }
    ]
  }
  ```

### 3. Analyze GitHub Repository
Fetches GitHub repository structure, analyzes tech stack, files, and architectural flows.
- **Route:** `POST /api/analyze`
- **Headers:** `Content-Type: application/json`
- **Request Body:**
  ```json
  {
    "url": "https://github.com/vercel/next.js"
  }
  ```
- **Response Structure (200 OK):**
  ```json
  {
    "result": {
      "repo": {
        "owner": "vercel",
        "name": "next.js",
        "fullName": "vercel/next.js",
        "description": "The React Framework",
        "stars": 128000,
        "defaultBranch": "canary"
      },
      "summary": "Full overview in chosen language...",
      "techStack": [
        { "name": "Next.js", "emoji": "▲", "reason": "Full-stack framework" }
      ],
      "folders": [ ... ],
      "files": [ ... ],
      "dataFlow": [ ... ]
    }
  }
  ```

---

## 🔐 Environment Variables

### Backend Configuration (`backend/.env` or Render Dashboard)

| Variable | Required | Description | Example |
| :--- | :---: | :--- | :--- |
| `DATABASE_URL` | **Yes** | PostgreSQL connection URL | `postgresql://user:pass@ep-xyz.aws.neon.tech/neondb?sslmode=require` |
| `PORT` | No | Port for Express server | `3001` (Render automatically injects `10000`) |
| `GITHUB_TOKEN` | Recommended | GitHub Personal Access Token (prevents rate limits) | `ghp_xxxxxxxxxxxxxxxxxxxx` |
| `REDIS_URL` | Optional | Redis connection URL for caching | `rediss://default:pass@endpoint.upstash.io:6379` |

### Frontend Configuration (`frontend/.env` or Cloudflare Pages)

| Variable | Required | Description | Example |
| :--- | :---: | :--- | :--- |
| `VITE_API_URL` | **Yes** | Public backend server API URL | `https://codeatlass-1.onrender.com` |

---

## 🚀 Quick Start (Local Setup)

### Prerequisites
- [Node.js](https://nodejs.org/) (v18 or higher)
- [Git](https://git-scm.com/)
- PostgreSQL database (Local or cloud instance like [Neon.tech](https://neon.tech))

### 1. Clone the Repository
```bash
git clone https://github.com/Mausam-Kumari9534/CodeAtlass.git
cd CodeAtlass
```

### 2. Setup Backend
```bash
cd backend

# Install dependencies
npm install

# Create .env file
cat <<EOF > .env
PORT=3001
DATABASE_URL=your_postgresql_connection_string
GITHUB_TOKEN=your_optional_github_token
REDIS_URL=your_optional_redis_url
EOF

# Run database migrations
npm run db:push

# Start development server
npm run dev
```
Backend will start on `http://localhost:3001`.

### 3. Setup Frontend
```bash
cd ../frontend

# Install dependencies
npm install

# Create .env file
echo "VITE_API_URL=http://localhost:3001" > .env

# Start Vite dev server
npm run dev
```
Open `http://localhost:5173` in your browser.

---

## ☁️ Production Deployment

### 1. Backend on Render
1. Create a new **Web Service** on [Render](https://render.com).
2. Connect your GitHub repository.
3. Configure build settings:
   - **Root Directory:** `backend`
   - **Build Command:** `npm install && npm run build`
   - **Start Command:** `npm start`
4. Under **Environment Variables**, add:
   - `DATABASE_URL`: Your Neon PostgreSQL Connection String
   - `GITHUB_TOKEN`: (Optional) GitHub Personal Access Token
   - `REDIS_URL`: (Optional) Upstash Redis URL

### 2. Frontend on Cloudflare Pages
1. Go to **Workers & Pages** in [Cloudflare Dashboard](https://dash.cloudflare.com).
2. Connect your repository.
3. Configure build settings:
   - **Framework Preset:** `Vite`
   - **Root Directory:** `frontend`
   - **Build Command:** `npm run build`
   - **Build output directory:** `dist`
4. Under **Settings > Environment variables**, add:
   - `VITE_API_URL`: `https://your-backend.onrender.com`
5. Trigger **Deploy**.

---

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

1. Fork the Project.
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`).
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`).
4. Push to the Branch (`git push origin feature/AmazingFeature`).
5. Open a Pull Request.

---

## 📄 License

This project is licensed under the **MIT License** — feel free to use it for personal and commercial projects.

<div align="center">
  <sub>Built with ❤️ by Mausam Kumari & Contributors</sub>
</div>
