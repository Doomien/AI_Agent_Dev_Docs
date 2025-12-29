# Microservice Development Workflow Guide
**Progressive Enhancement: Static Files → Homelab → Docker → GitHub**

---

## Overview

This workflow enables rapid iteration on small web projects (recipe apps, games, utilities) starting with simple HTML/CSS/JS files, graduating to containerized services only when complexity demands it.

### Philosophy
- **Start simple**: Plain files on shared web server
- **Add complexity incrementally**: Only containerize when needed
- **Stay portable**: Always ready to share via GitHub

---

## Step 1: Local Development

### Setup
- Create project folder: `~/projects/project-name/`
- Open in VSCode
- Use Live Server extension for local testing (`http://localhost:5500`)

### Project Structure (Level 0)
```
project-name/
├── index.html
├── styles.css
├── app.js
└── assets/
    └── images/
```

### Development Process
1. Write code in VSCode
2. Test with Live Server (auto-refresh on save)
3. Iterate until core functionality works

### Exit Criteria
- ✅ Core functionality works
- ✅ No obvious bugs in local testing
- ✅ Ready to test in real environment

---

## Step 2: Homelab Deployment & Refinement

### Initial Homelab Setup (One-Time)

**Create shared static server:**
```bash
# On homelab server
mkdir -p /srv/www
cd /srv/www

cat > docker-compose.yml <<EOF
version: '3.8'
services:
  static-server:
    image: nginx:alpine
    container_name: static-apps
    volumes:
      - ./:/usr/share/nginx/html:ro
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
    restart: unless-stopped
    ports:
      - "8080:80"
EOF

cat > nginx.conf <<EOF
server {
    listen 80;
    root /usr/share/nginx/html;
    autoindex on;
    
    location / {
        try_files \$uri \$uri/ =404;
    }
}
EOF

docker-compose up -d
```

**Create optional landing page:**
```bash
cat > /srv/www/index.html <<EOF
<!DOCTYPE html>
<html>
<head>
    <title>Homelab Apps</title>
    <style>
        body { font-family: system-ui; max-width: 800px; margin: 50px auto; padding: 20px; }
        ul { list-style: none; padding: 0; }
        li { margin: 10px 0; }
        a { color: #0066cc; text-decoration: none; font-size: 18px; }
        a:hover { text-decoration: underline; }
    </style>
</head>
<body>
    <h1>🏠 Homelab Apps</h1>
    <ul id="apps"></ul>
    <script>
        // Auto-discover apps (excluding index.html and nginx.conf)
        fetch('./')
            .then(r => r.text())
            .then(html => {
                const parser = new DOMParser();
                const doc = parser.parseFromString(html, 'text/html');
                const links = Array.from(doc.querySelectorAll('a'))
                    .filter(a => a.href.endsWith('/') && !a.href.includes('..'))
                    .map(a => a.textContent.replace('/', ''));
                
                const list = document.getElementById('apps');
                links.forEach(name => {
                    const li = document.createElement('li');
                    li.innerHTML = \`<a href="/\${name}/">\${name}</a>\`;
                    list.appendChild(li);
                });
            });
    </script>
</body>
</html>
EOF
```

### Deploy to Shared Static Server

**Option A: Manual Deploy**
```bash
rsync -avz ~/projects/project-name/ homelab:/srv/www/project-name/
```

**Option B: VSCode Task (Recommended)**

Create `.vscode/tasks.json` in your project:
```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Deploy to Homelab",
      "type": "shell",
      "command": "rsync -avz --delete ./ homelab:/srv/www/${workspaceFolderBasename}/",
      "problemMatcher": [],
      "group": {
        "kind": "build",
        "isDefault": true
      }
    }
  ]
}
```

Deploy with: `Ctrl+Shift+B` (or `Cmd+Shift+B` on Mac)

**Option C: Watch & Auto-Sync (Active Development)**

Create `~/bin/watch-and-deploy.sh`:
```bash
#!/bin/bash
PROJECT_DIR=${1:?Usage: watch-and-deploy.sh ~/projects/my-app}
PROJECT_NAME=$(basename $PROJECT_DIR)

# macOS: install with 'brew install fswatch'
# Linux: install with 'apt install inotify-tools' and use 'inotifywait' instead

fswatch -o $PROJECT_DIR | while read; do
  rsync -avz --delete $PROJECT_DIR/ homelab:/srv/www/$PROJECT_NAME/
  echo "✓ Synced at $(date '+%H:%M:%S')"
done
```

Usage:
```bash
chmod +x ~/bin/watch-and-deploy.sh
~/bin/watch-and-deploy.sh ~/projects/recipe-app
```

### Access & Test
- **URL**: `http://homelab:8080/project-name/`
- Test on multiple devices (phone, tablet, different browsers)
- Gather real-world usage feedback

### Iteration Loop
1. Edit files locally in VSCode
2. Deploy to homelab (manual, VSCode task, or auto-sync)
3. Test live version
4. Repeat until stable

---

### Level 1 Upgrade: Add Backend API (Optional)

**When you need:**
- Data persistence (save/load)
- Server-side logic
- API integration
- Simple database operations

**Implementation:**

**1. Create API backend:**
```bash
# On homelab
mkdir -p /srv/www/_api
cd /srv/www/_api

# Example: Simple Node.js API
cat > recipe-api.js <<EOF
const express = require('express');
const fs = require('fs').promises;
const app = express();

app.use(express.json());

// Simple JSON file storage
const DATA_FILE = './recipes.json';

app.get('/recipes', async (req, res) => {
  const data = await fs.readFile(DATA_FILE, 'utf8').catch(() => '[]');
  res.json(JSON.parse(data));
});

app.post('/recipes', async (req, res) => {
  const recipes = JSON.parse(await fs.readFile(DATA_FILE, 'utf8').catch(() => '[]'));
  recipes.push(req.body);
  await fs.writeFile(DATA_FILE, JSON.stringify(recipes, null, 2));
  res.json({ success: true });
});

app.listen(3001, () => console.log('API running on :3001'));
EOF

npm install express
npm install -g pm2
pm2 start recipe-api.js
pm2 save
```

**2. Configure nginx to proxy API:**
```bash
# Add to /srv/www/nginx.conf
location /recipe-app/api/ {
    proxy_pass http://host.docker.internal:3001/;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```

**3. Update frontend to use API:**
```javascript
// In your app.js
async function loadRecipes() {
  const response = await fetch('/recipe-app/api/recipes');
  const recipes = await response.json();
  // ... render recipes
}

async function saveRecipe(recipe) {
  await fetch('/recipe-app/api/recipes', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(recipe)
  });
}
```

---

## Step 3: Elevate to Container & Publish to GitHub

### When to Elevate

**Upgrade when you need:**
- ✅ Database (SQLite, PostgreSQL, MongoDB, etc.)
- ✅ Scheduled tasks/cron jobs
- ✅ User authentication/sessions
- ✅ Multiple backend services
- ✅ Environment-specific configuration
- ✅ Want to share project publicly
- ✅ Complex dependencies

**Stay at Level 0/1 when:**
- ❌ Simple static content only
- ❌ Minimal or no backend logic
- ❌ Working fine as-is
- ❌ Just for personal use

---

### Containerization Process

#### 1. Restructure Project

**From:**
```
project-name/
├── index.html
├── styles.css
├── app.js
└── assets/
```

**To:**
```
project-name/
├── Dockerfile
├── docker-compose.yml
├── .dockerignore
├── .gitignore
├── .env.example
├── README.md
├── public/              # Your existing frontend files
│   ├── index.html
│   ├── styles.css
│   ├── app.js
│   └── assets/
├── server/              # Backend (if needed)
│   ├── index.js
│   ├── package.json
│   └── routes/
└── data/                # Databases, uploads, etc. (gitignored)
    └── .gitkeep
```

#### 2. Create Dockerfile

**For static site (nginx):**
```dockerfile
FROM nginx:alpine

# Copy static files
COPY public/ /usr/share/nginx/html/

# Optional: custom nginx config
# COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget --quiet --tries=1 --spider http://localhost/ || exit 1
```

**For Node.js backend:**
```dockerfile
FROM node:20-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install production dependencies
RUN npm ci --only=production

# Copy application code
COPY . .

EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD node -e "require('http').get('http://localhost:3000/health', (r) => process.exit(r.statusCode === 200 ? 0 : 1))"

CMD ["node", "server/index.js"]
```

**For full-stack (multi-stage):**
```dockerfile
# Build stage for frontend (if using build tools)
FROM node:20-alpine AS frontend-build
WORKDIR /app
COPY public/package*.json ./
RUN npm ci
COPY public/ ./
RUN npm run build

# Final stage
FROM node:20-alpine
WORKDIR /app

# Backend dependencies
COPY server/package*.json ./
RUN npm ci --only=production

# Copy backend
COPY server/ ./

# Copy built frontend
COPY --from=frontend-build /app/dist ./public

EXPOSE 3000
CMD ["node", "index.js"]
```

#### 3. Create docker-compose.yml

**Simple (single service):**
```yaml
version: '3.8'

services:
  app:
    build: .
    container_name: project-name
    ports:
      - "8081:80"  # or 3000 for Node.js
    restart: unless-stopped
    environment:
      - NODE_ENV=production
    # volumes:
    #   - ./data:/app/data  # For persistent data
```

**With database:**
```yaml
version: '3.8'

services:
  app:
    build: .
    container_name: project-name
    ports:
      - "8081:3000"
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgres://user:pass@db:5432/dbname
    depends_on:
      - db
    volumes:
      - ./data/uploads:/app/uploads

  db:
    image: postgres:16-alpine
    container_name: project-name-db
    restart: unless-stopped
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=dbname
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

#### 4. Create Support Files

**.dockerignore:**
```
node_modules
npm-debug.log
.git
.gitignore
.env
*.md
.vscode
.DS_Store
data/*.db
data/uploads/*
!data/.gitkeep
```

**.gitignore:**
```
node_modules/
.env
*.log
.DS_Store
data/*.db
data/uploads/*
!data/.gitkeep
dist/
build/
```

**.env.example:**
```bash
# Database
DATABASE_URL=postgres://user:password@localhost:5432/dbname

# API Keys (replace with your own)
API_KEY=your_api_key_here

# Application
PORT=3000
NODE_ENV=development
```

#### 5. Create Comprehensive README.md

```markdown
# Project Name

Brief description of what this project does.

## Features

- Feature 1
- Feature 2
- Feature 3

## Quick Start

### Prerequisites
- Docker & Docker Compose
- (Any other requirements)

### Installation

1. Clone the repository:
   \`\`\`bash
   git clone https://github.com/yourusername/project-name.git
   cd project-name
   \`\`\`

2. Copy environment file:
   \`\`\`bash
   cp .env.example .env
   \`\`\`

3. Edit \`.env\` with your configuration

4. Start with Docker:
   \`\`\`bash
   docker-compose up -d
   \`\`\`

5. Access at: http://localhost:8081

## Development

### Local Development
\`\`\`bash
cd server
npm install
npm run dev
\`\`\`

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| PORT | Server port | 3000 |
| DATABASE_URL | Database connection string | - |
| NODE_ENV | Environment | development |

## Project Structure

\`\`\`
project-name/
├── public/         # Frontend files
├── server/         # Backend code
├── data/           # Persistent data (gitignored)
└── docker-compose.yml
\`\`\`

## API Documentation

### GET /api/items
Returns list of items.

**Response:**
\`\`\`json
{
  "items": [...]
}
\`\`\`

## Screenshots

![Screenshot 1](screenshots/screenshot1.png)

## Contributing

Contributions welcome! Please open an issue first.

## License

MIT
```

#### 6. Test Docker Build Locally

```bash
# Build the image
docker-compose build

# Start services
docker-compose up -d

# Check logs
docker-compose logs -f

# Test the application
curl http://localhost:8081
# Or open in browser

# Stop services
docker-compose down
```

#### 7. Publish to GitHub

```bash
# Initialize git (if not already)
git init

# Add all files
git add .

# First commit
git commit -m "Initial commit - containerized version"

# Create GitHub repo and push
gh repo create project-name --public --source=. --push

# Or manually:
# git remote add origin https://github.com/yourusername/project-name.git
# git branch -M main
# git push -u origin main
```

#### 8. Deploy to Homelab (Containerized)

**Method A: Git clone (recommended):**
```bash
# On homelab
cd /srv/projects
git clone https://github.com/yourusername/project-name.git
cd project-name

# Setup environment
cp .env.example .env
nano .env  # Edit with production values

# Start services
docker-compose up -d

# Check logs
docker-compose logs -f
```

**Method B: Direct deployment:**
```bash
# From laptop
rsync -avz --exclude 'node_modules' --exclude '.git' \
  ~/projects/project-name/ homelab:/srv/projects/project-name/

# On homelab
cd /srv/projects/project-name
docker-compose up -d
```

---

## Step 4: Continued Refinement

### Development Workflow (Post-Docker)

**On laptop:**
```bash
cd ~/projects/project-name

# Make changes
git add .
git commit -m "Add feature X"
git push
```

**On homelab:**
```bash
cd /srv/projects/project-name
git pull
docker-compose up -d --build
```

### Automated Deployment (Optional)

**Setup git webhook or cron job:**

Create `/srv/scripts/auto-update.sh`:
```bash
#!/bin/bash
cd /srv/projects/project-name
git pull
docker-compose up -d --build
```

**Option A: GitHub webhook** (requires exposing homelab)
**Option B: Cron job** (simpler):
```bash
# Check for updates every 15 minutes
*/15 * * * * /srv/scripts/auto-update.sh >> /var/log/auto-update.log 2>&1
```

### Maintenance Tasks

**Monitor services:**
```bash
# View logs
docker-compose logs -f

# Check status
docker-compose ps

# Check resource usage
docker stats
```

**Update dependencies:**
```bash
# Update base images
docker-compose pull
docker-compose up -d

# Update npm packages (in development)
npm update
npm audit fix
```

**Backup data:**
```bash
# Backup data directory
tar -czf backup-$(date +%Y%m%d).tar.gz data/

# Or backup database
docker-compose exec db pg_dump -U user dbname > backup.sql
```

**Clean up:**
```bash
# Remove unused images
docker image prune -a

# Remove unused volumes
docker volume prune
```

### Iteration Patterns

**Small fixes/features:**
1. Edit locally
2. Test with `docker-compose up`
3. Commit and push
4. Pull on homelab

**Major refactoring:**
1. Create feature branch: `git checkout -b feature-name`
2. Develop and test locally
3. Merge to main when ready
4. Deploy to homelab

**User feedback loop:**
1. Collect issues on GitHub
2. Prioritize based on usage
3. Deploy updates regularly
4. Tag releases: `git tag v1.0.0`

---

## Quick Reference

### Promotion Decision Tree

```
Is it just HTML/CSS/JS with no backend?
  → YES: Stay at Step 2 (shared static server)
  
Need backend API?
  → Simple (<100 lines, no DB): Add PM2 backend at Step 2 (Level 1)
  → Complex (with DB, auth): Move to Step 3 (containerize)

Need database?
  → YES: Move to Step 3

Need scheduled tasks/cron?
  → YES: Move to Step 3

Want to share publicly?
  → YES: Move to Step 3

Works well as-is?
  → YES: Stay where you are!
```

### Common Commands Cheat Sheet

**SSH Config** (`~/.ssh/config`):
```
Host homelab
    HostName 192.168.1.x
    User damien
    IdentityFile ~/.ssh/id_ed25519
```

**Deploy static files:**
```bash
rsync -avz ~/projects/project-name/ homelab:/srv/www/project-name/
```

**Deploy Docker project:**
```bash
rsync -avz --exclude 'node_modules' ~/projects/project-name/ homelab:/srv/projects/project-name/
ssh homelab "cd /srv/projects/project-name && docker-compose up -d --build"
```

**View logs:**
```bash
ssh homelab "cd /srv/projects/project-name && docker-compose logs -f"
```

**Restart service:**
```bash
ssh homelab "cd /srv/projects/project-name && docker-compose restart"
```

---

## Helper Scripts

### ~/bin/new-project.sh
```bash
#!/bin/bash
# Create new static project from template

PROJECT=${1:?Usage: new-project.sh project-name}
TEMPLATE_DIR=~/templates/static-app

# Create project directory
mkdir -p ~/projects/$PROJECT

# Copy template files
if [ -d "$TEMPLATE_DIR" ]; then
  cp -r $TEMPLATE_DIR/* ~/projects/$PROJECT/
else
  # Create basic structure
  mkdir -p ~/projects/$PROJECT/assets
  
  cat > ~/projects/$PROJECT/index.html <<EOF
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>$PROJECT</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <h1>$PROJECT</h1>
    <script src="app.js"></script>
</body>
</html>
EOF

  cat > ~/projects/$PROJECT/styles.css <<EOF
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: system-ui, -apple-system, sans-serif;
    padding: 20px;
    max-width: 1200px;
    margin: 0 auto;
}

h1 {
    margin-bottom: 20px;
}
EOF

  cat > ~/projects/$PROJECT/app.js <<EOF
// $PROJECT
console.log('$PROJECT loaded');
EOF

  # Create VSCode task
  mkdir -p ~/projects/$PROJECT/.vscode
  cat > ~/projects/$PROJECT/.vscode/tasks.json <<EOF
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Deploy to Homelab",
      "type": "shell",
      "command": "rsync -avz --delete ./ homelab:/srv/www/$PROJECT/",
      "problemMatcher": [],
      "group": {
        "kind": "build",
        "isDefault": true
      }
    }
  ]
}
EOF
fi

echo "✓ Project created at ~/projects/$PROJECT"
echo "→ Opening in VSCode..."
code ~/projects/$PROJECT
```

### ~/bin/deploy-static.sh
```bash
#!/bin/bash
# Deploy static project to homelab

PROJECT=${1:?Usage: deploy-static.sh project-name}

if [ ! -d ~/projects/$PROJECT ]; then
  echo "Error: Project ~/projects/$PROJECT does not exist"
  exit 1
fi

echo "Deploying $PROJECT to homelab..."
rsync -avz --delete \
  --exclude '.git' \
  --exclude '.vscode' \
  --exclude 'node_modules' \
  ~/projects/$PROJECT/ \
  homelab:/srv/www/$PROJECT/

echo "✓ Deployed successfully"
echo "→ http://homelab:8080/$PROJECT/"
```

### ~/bin/promote-to-docker.sh
```bash
#!/bin/bash
# Promote static project to Docker-ready structure

PROJECT=${1:?Usage: promote-to-docker.sh project-name}
PROJECT_DIR=~/projects/$PROJECT

if [ ! -d "$PROJECT_DIR" ]; then
  echo "Error: Project $PROJECT_DIR does not exist"
  exit 1
fi

echo "Promoting $PROJECT to Docker structure..."

# Create new directory structure
mkdir -p $PROJECT_DIR/public
mkdir -p $PROJECT_DIR/data
touch $PROJECT_DIR/data/.gitkeep

# Move existing files to public/
mv $PROJECT_DIR/*.html $PROJECT_DIR/public/ 2>/dev/null
mv $PROJECT_DIR/*.css $PROJECT_DIR/public/ 2>/dev/null
mv $PROJECT_DIR/*.js $PROJECT_DIR/public/ 2>/dev/null
mv $PROJECT_DIR/assets $PROJECT_DIR/public/ 2>/dev/null

# Create Dockerfile
cat > $PROJECT_DIR/Dockerfile <<EOF
FROM nginx:alpine
COPY public/ /usr/share/nginx/html/
EXPOSE 80
HEALTHCHECK --interval=30s --timeout=3s \\
  CMD wget --quiet --tries=1 --spider http://localhost/ || exit 1
EOF

# Create docker-compose.yml
cat > $PROJECT_DIR/docker-compose.yml <<EOF
version: '3.8'

services:
  app:
    build: .
    container_name: $PROJECT
    ports:
      - "8081:80"
    restart: unless-stopped
EOF

# Create .dockerignore
cat > $PROJECT_DIR/.dockerignore <<EOF
node_modules
.git
.gitignore
.env
*.md
.vscode
.DS_Store
data/*
!data/.gitkeep
EOF

# Create .gitignore
cat > $PROJECT_DIR/.gitignore <<EOF
node_modules/
.env
*.log
.DS_Store
data/*
!data/.gitkeep
EOF

# Create README.md
cat > $PROJECT_DIR/README.md <<EOF
# $PROJECT

## Quick Start

\`\`\`bash
docker-compose up -d
\`\`\`

Access at http://localhost:8081

## Development

Edit files in \`public/\` directory.

## Deployment

\`\`\`bash
git clone <repo-url>
cd $PROJECT
docker-compose up -d
\`\`\`
EOF

echo "✓ Project promoted to Docker structure"
echo ""
echo "Next steps:"
echo "1. cd ~/projects/$PROJECT"
echo "2. Test: docker-compose up"
echo "3. Initialize git: git init"
echo "4. Commit: git add . && git commit -m 'Initial commit'"
echo "5. Push to GitHub: gh repo create $PROJECT --public --source=. --push"
```

### Make scripts executable
```bash
chmod +x ~/bin/new-project.sh
chmod +x ~/bin/deploy-static.sh
chmod +x ~/bin/promote-to-docker.sh
```

---

## Project Templates

### Create Template Directory

```bash
mkdir -p ~/templates/static-app/{assets,.vscode}
```

### Template: index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>App Title</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>Welcome</h1>
        <p>Start building your app here.</p>
    </div>
    <script src="app.js"></script>
</body>
</html>
```

### Template: styles.css
```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

:root {
    --primary-color: #0066cc;
    --background: #ffffff;
    --text: #333333;
    --border: #dddddd;
}

body {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    line-height: 1.6;
    color: var(--text);
    background: var(--background);
}

.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
}

h1 {
    margin-bottom: 20px;
    color: var(--primary-color);
}

/* Mobile-first responsive */
@media (max-width: 768px) {
    .container {
        padding: 10px;
    }
}
```

### Template: app.js
```javascript
// Main application logic
console.log('App loaded');

// Example: Simple state management
const app = {
  init() {
    console.log('Initializing app...');
    this.setupEventListeners();
  },

  setupEventListeners() {
    // Add your event listeners here
  },

  // Add your methods here
};

// Initialize when DOM is ready
if (document.readyState === 'loading') {
  document.addEventListener('DOMContentLoaded', () => app.init());
} else {
  app.init();
}
```

### Template: .vscode/tasks.json
```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Deploy to Homelab",
      "type": "shell",
      "command": "rsync -avz --delete ./ homelab:/srv/www/${workspaceFolderBasename}/",
      "problemMatcher": [],
      "group": {
        "kind": "build",
        "isDefault": true
      }
    }
  ]
}
```

---

## Troubleshooting

### Common Issues

**rsync: command not found**
```bash
# macOS
brew install rsync

# Linux
sudo apt install rsync
```

**SSH connection issues**
```bash
# Test connection
ssh homelab

# If fails, add to ~/.ssh/config:
Host homelab
    HostName 192.168.1.x
    User your-username
    IdentityFile ~/.ssh/id_rsa
```

**Docker permission denied**
```bash
# Add user to docker group (Linux)
sudo usermod -aG docker $USER
# Log out and back in
```

**Port already in use**
```bash
# Find what's using the port
lsof -i :8080

# Change port in docker-compose.yml
ports:
  - "8082:80"  # Use different port
```

**Changes not appearing on homelab**
```bash
# Check rsync actually copied files
ls -la homelab:/srv/www/project-name/

# Check nginx is serving correct directory
docker exec static-apps ls /usr/share/nginx/html/

# Force browser refresh (clear cache)
Ctrl+Shift+R (or Cmd+Shift+R)
```

### Performance Tips

**Optimize rsync:**
```bash
# Exclude unnecessary files
rsync -avz \
  --exclude '.git' \
  --exclude 'node_modules' \
  --exclude '.DS_Store' \
  ~/projects/project-name/ homelab:/srv/www/project-name/
```

**Compress Docker images:**
```dockerfile
# Use alpine variants
FROM node:20-alpine  # Instead of FROM node:20

# Multi-stage builds
FROM node:20-alpine AS build
# ... build steps
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
```

---

## Appendix: Example Projects

### Example 1: Recipe Storage App

**Level 0 (Static):**
- HTML form for recipe input
- LocalStorage for persistence
- Client-side search/filter

**Level 1 (+ Backend):**
- Express API for recipe CRUD
- JSON file storage
- Shared PM2 process

**Level 2 (Dockerized):**
- SQLite database
- Image upload support
- User authentication
- Published to GitHub

### Example 2: Pixel Art Game

**Level 0 (Static):**
- HTML5 Canvas
- Vanilla JavaScript game loop
- No persistence needed
- Lives happily at Level 0 forever!

### Example 3: Budget Tracker

**Level 0 (Static):**
- Form for expense entry
- LocalStorage for data
- Chart.js for visualizations

**Level 2 (Skip Level 1):**
- Jumped straight to Docker
- PostgreSQL database
- CSV export feature
- Multi-user with auth

---

## Resources

### Documentation
- [Docker Docs](https://docs.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)
- [nginx Documentation](https://nginx.org/en/docs/)
- [GitHub CLI](https://cli.github.com/)

### Tools
- [VSCode](https://code.visualstudio.com/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop)
- [Portainer](https://www.portainer.io/) - Docker management UI
- [Traefik](https://traefik.io/) - Reverse proxy for Docker

### Learning
- [Docker Getting Started](https://docs.docker.com/get-started/)
- [nginx Beginner's Guide](https://nginx.org/en/docs/beginners_guide.html)
- [MDN Web Docs](https://developer.mozilla.org/)

---

## Version History

- **v1.0** - Initial workflow guide
- Created: 2024

---

**License**: Feel free to use and modify this guide for your own projects.
