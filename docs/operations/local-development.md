# Local development

## Prerequisites

- Node.js 20+ and npm
- Docker Desktop for Windows with Linux containers
- PowerShell
- Python 3 only when building this MkDocs documentation site locally

The workspace is a container directory, not a Git repository for the individual applications. Run package commands from `marketplace-api`, `marketplace-web`, or `marketplace-docs`.

## Start the application

```powershell
# Terminal 1 — Supabase
Set-Location "C:\Workspace\marketpalce application\marketplace-api"
npm.cmd ci
npm.cmd run supabase:start

# Terminal 2 — Express API
Set-Location "C:\Workspace\marketpalce application\marketplace-api"
npm.cmd run dev

# Terminal 3 — Next.js web app
Set-Location "C:\Workspace\marketpalce application\marketplace-web"
npm.cmd ci
npm.cmd run dev
```

Open `http://localhost:3000`. Verify the Express process separately at `http://localhost:4000/api/health`.

## Configuration

Create untracked local environment files from the provided examples.

| Package | Variable | Purpose |
| --- | --- | --- |
| API | `PORT` | Express listener, default `4000`. |
| API | `CORS_ORIGIN` / `CORS_ORIGINS` | Trusted browser origins. |
| API | `SUPABASE_URL`, `SUPABASE_PUBLISHABLE_KEY`, `SUPABASE_SECRET_KEY` | Local/hosted Supabase connection; secret key stays server-side. |
| API | `RATE_LIMIT_WINDOW_MS`, `RATE_LIMIT_MAX` | API throttling configuration. |
| Web | `MARKETPLACE_API_URL` | Server-only Express upstream for the gateway. |

For local Supabase, use the URLs and generated keys returned by `npm.cmd run supabase:status`. The usual local services are API/Auth at `54321`, PostgreSQL at `54322`, Studio at `54323`, and email viewer at `54324`.

## Database migration workflow

```powershell
Set-Location "C:\Workspace\marketpalce application\marketplace-api"
npx.cmd supabase migration up --local
npm.cmd run supabase:lint
```

`supabase db reset` recreates local data, reapplies all migrations, and imports seeds. Use it only when that destructive reset is intended—not during buyer/seller acceptance testing.

## Build this documentation site

```powershell
Set-Location "C:\Workspace\marketpalce application\marketplace-docs"
py -m pip install -r requirements.txt
py -m mkdocs build --strict
py -m mkdocs serve
```

The generated `site/` directory is a build artifact. It is the content that GitHub Pages receives through the workflow; do not configure Pages to serve raw Markdown from the repository root.
