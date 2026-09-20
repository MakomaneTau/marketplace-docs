# Marketplace documentation

The GitHub Pages-ready engineering guide for the Marketplace application.

It documents the independently maintained Next.js web application, Express/Supabase API, database architecture, API contract, security model, development workflow, testing, deployment, and contribution boundaries.

## Local preview

```powershell
py -m pip install -r requirements.txt
py -m mkdocs serve
```

Build the production site and fail on broken links/configuration:

```powershell
py -m mkdocs build --strict
```

GitHub Actions builds and publishes `site/` to GitHub Pages on pushes to `main`. Configure the repository's Pages source as **GitHub Actions**.
