# BLT-Sammich Slack Bot

**BLT-Sammich** is a feature-rich, serverless Slack bot built for the OWASP BLT (Bug Logging Tool) community. Operating on the edge via Cloudflare Workers, this bot helps teams interact with GitHub repositories, track contributors, manage projects, and discover OWASP resources directly from Slack with near-zero latency.

> **Note:** The core functionality of this bot has been decoupled from the [main BLT repository](https://github.com/OWASP-BLT/BLT). This repository serves as the standalone, edge-optimized implementation of the BLT Slack bot.

## 📋 Table of Contents

- [Features & Commands](#-features--commands)
- [Architecture](#-architecture)
- [Local Development Setup](#-local-development-setup)
- [Project Structure](#-project-structure)
- [Contributing](#-contributing)

## ✨ Features & Commands

BLT-Sammich handles incoming Slack webhooks and slash commands natively at the edge. 

### Core Slash Commands

* `/contributors` - Displays contributor activity for the `OWASP-BLT/Lettuce` repository over the last 7 days. Fetches merged PRs, resolved issues, and comment metrics.
* `/ghissue [title]` - Creates a new GitHub issue directly from Slack in the configured repository. Returns a direct link to the newly created issue.
* `/project [project_name]` - Retrieves detailed information about OWASP projects from the internal dataset. Supports interactive dropdowns for broad searches.
* `/repo [technology]` - Finds relevant OWASP repositories based on the requested technology stack (e.g., Python, JavaScript, Rust).

## 🏗️ Architecture

### Tech Stack
* **Edge Runtime:** Cloudflare Workers (Python via Pyodide)
* **Database:** Cloudflare D1 (Serverless SQLite)
* **Deployment & Testing:** Wrangler (`npx wrangler`)
* **Integrations:** Slack Webhooks & GitHub REST API

### System Diagram

```text
┌─────────────────────────────────────────────────────┐
│                  User in Slack                      │
└─────────────────┬───────────────────────────────────┘
                  │ (Slash Commands / Events)
                  v
         ┌────────────────────┐
         │ Cloudflare Worker  │  <-- Python (Pyodide)
         │   (BLT-Sammich)    │
         └─┬────────────────┬─┘
           │                │
           v                v
  ┌────────────────┐ ┌─────────────────────┐
  │ Cloudflare D1  │ │     GitHub API      │
  │ (Local SQLite) │ │ OWASP-BLT/Lettuce   │
  └────────────────┘ └─────────────────────┘
```

## 🚀 Local Development Setup

### Prerequisites
- [Node.js](https://nodejs.org/) (latest LTS)
- [Cloudflare Wrangler](https://developers.cloudflare.com/workers/wrangler/install-and-update/) (`npm install -g wrangler`)
- Slack workspace with admin access
- GitHub account and Personal Access Token

### 1. Initialize the Environment
Clone the repository and enter the directory:
```powershell
git clone https://github.com/OWASP-BLT/BLT-Sammich.git
cd BLT-Sammich
```

### 2. Configure Secrets
Create a `.env` file (or use Wrangler secrets) with your credentials:
```powershell
SLACK_SIGNING_SECRET=your-secret
SLACK_BOT_TOKEN=xoxb-your-token
GITHUB_TOKEN=ghp_your-token
```

### 3. Initialize the Local Database (D1)
Run the migration file to set up your local serverless SQLite instance:
```powershell
npx wrangler d1 execute blt-sammich --local --file=migrations/0001_initial.sql
```

### 4. Run the Dev Server
```powershell
npx wrangler dev
```

## 📂 Project Structure
* `src/worker.py` - Core logic for handle_request and Slack event routing.
* `public/` - Static assets served by the worker.
* `migrations/` - SQL schema definitions for the D1 database.
* `data/` - Bundled JSON datasets for projects and repositories.

## 🤝 Contributing
We welcome contributions! Please refer to the [OWASP BLT main repository](https://github.com/OWASP-BLT/BLT) for general contribution guidelines.
