# az-devops-skillpulse
Azure-SkillPulse-project

# 📊 SkillPulse

SkillPulse is a **skill tracking and learning dashboard** built with Go, MySQL, Nginx, and Docker. It helps users log learning sessions and track progress over time through a simple web interface and REST API.

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────┐
│                   Browser                     │
│              http://localhost                 │
└──────────────────┬───────────────────────────┘
                   │ Port 80
┌──────────────────▼───────────────────────────┐
│                  Nginx                        │
│         (Reverse Proxy + Static Files)        │
│                                               │
│   /          → Serves HTML/CSS/JS (frontend)  │
│   /api/*     → Proxies to Go backend          │
│   /health    → Proxies to Go backend          │
└──────────────────┬───────────────────────────┘
                   │ Port 8080 (internal)
┌──────────────────▼───────────────────────────┐
│              Go Backend (Gin)                  │
│            REST API on :8080                   │
│                                                │
│   GET    /api/skills      → List all skills    │
│   POST   /api/skills      → Add a skill        │
│   GET    /api/skills/:id  → Skill details      │
│   DELETE /api/skills/:id  → Delete a skill     │
│   POST   /api/skills/:id/log → Log session     │
│   GET    /api/dashboard   → Dashboard stats    │
│   GET    /health          → Health check       │
└──────────────────┬───────────────────────────┘
                   │ Port 3306 (internal)
┌──────────────────▼───────────────────────────┐
│               MySQL 8.0                       │
│         Database: skillpulse                  │
│                                               │
│   Tables: skills, learning_logs               │
└──────────────────────────────────────────────┘
```

---

## Project Structure

```
az-devops-skillpulse/
├── backend/
│   ├── main.go              # Entry point
│   ├── go.mod               # Go module
│   ├── handlers/
│   │   ├── skills.go        # Skill CRUD
│   │   ├── logs.go          # Learning logs
│   │   └── dashboard.go     # Dashboard + health
│   ├── models/
│   │   └── skill.go         # Data structures
│   ├── database/
│   │   └── db.go            # MySQL connection
│   └── Dockerfile           # Multi-stage build
├── frontend/
│   ├── index.html           # Dashboard page
│   ├── css/style.css        # Styles
│   └── js/app.js            # Frontend logic
├── nginx/nginx.conf          # Reverse proxy config
├── mysql/init.sql            # Schema + seed data
├── docker-compose.yml        # Orchestration
└── .env.example              # Env vars template
```

---


## Run It Locally

```bash
cd project/skillpulse
cp .env.example .env
docker-compose up --build
```

Open **http://localhost** — you should see the dashboard with seed data (5 pre-loaded skills).

**Try it out:**
1. Add a new skill — click "+ Add Skill"
2. Log a session — click "Log Session" on any skill
3. Watch the progress bar update
4. Check the API: http://localhost/api/skills
5. Health check: http://localhost/health → `{"status": "healthy"}`

**Stop:**

```bash
docker-compose down       # Stop containers
docker-compose down -v    # Stop + remove database data
```
---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Port 80 in use | Stop other web servers: `sudo lsof -i :80` |
| Backend can't connect to DB | Wait 30 seconds — it retries automatically |
| init.sql didn't run | `docker-compose down -v` then rebuild |
| Frontend not updating | Hard refresh: Cmd+Shift+R / Ctrl+Shift+R |
| Docker permission denied | `sudo usermod -aG docker $USER`, restart terminal |

---

## Acknowledgment

> This project was developed as part of a tutorial from the TrainWithShubham community by Shubham Londhe.
