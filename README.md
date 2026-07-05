# Todoist → Notion Backlog Workflow

Sync new Todoist tasks from your **Backlog** project into a Notion **Backlog** database, automatically linking each ticket to an existing epic or creating a new one when no match is found.

Built with [n8n](https://n8n.io/) running in Docker.

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- A [Notion](https://www.notion.so/) workspace with:
  - A **Backlog** database (tickets)
  - An **Epics** database
  - A [Notion internal integration](https://www.notion.so/my-integrations) with access to both databases
- A [Todoist](https://todoist.com/) account with a project named **Backlog** and a [Todoist API token](https://todoist.com/app/settings/integrations/developer)

## Quick start

### 1. Clone and configure

```powershell
git clone https://github.com/YOUR_USERNAME/todoist-to-notion.git
cd todoist-to-notion
```

### 2. Set your environment variables

```powershell
copy .env.example .env
```

Then open `.env` and replace the placeholders:

```
N8N_USER=admin
N8N_PASSWORD=your-secure-password
```

### 3. Start n8n

```powershell
docker compose up -d
```

n8n will be available at **http://localhost:5678**. Log in with the username and password you set in `.env`.

### 4. Configure n8n credentials

Once logged into n8n:

1. Go to **Settings → Credentials**
2. Add a **Todoist API** credential with your Todoist API token
3. Add a **Notion API** credential with your Notion internal integration token

### 5. Import the workflow

1. In n8n, click **Import from File**
2. Select `workflows/todoist-to-notion.json`
3. Update the placeholder database IDs in the Notion nodes:
   - `YOUR_BACKLOG_DB_ID` — your Notion Backlog database ID
   - `YOUR_EPICS_DB_ID` — your Notion Epics database ID
4. Click **Activate** to start the workflow

## How it works

```
Todoist "Backlog" task
        │
        ▼
[TODOIST TRIGGER] ─── Watches for new tasks
        │
        ▼
[NOTION SEARCH]  ─── Searches Epics DB by keyword
        │
    ┌───┴───┐
    │  IF   │  ─── Epic found?
    └───┬───┘
    No       Yes
    │         │
    ▼         │
[CREATE EPIC] │
    │         │
    └────┬────┘
         ▼
[CREATE BACKLOG TICKET] ─── Linked to epic
```

## Project structure

```
todoist-to-notion/
├── docker-compose.yaml      # n8n Docker service
├── .env                     # Secrets (git-ignored)
├── .env.example             # Safe-to-commit template
├── .gitignore
├── workflows/
│   ├── .gitkeep
│   └── todoist-to-notion.json   # Exported n8n workflow
└── README.md
```

## Stopping

```powershell
docker compose down
```
