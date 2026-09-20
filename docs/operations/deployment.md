# Deployment and GitHub Pages

## Application deployment boundaries

| Component | Deployable unit | Key production concern |
| --- | --- | --- |
| Web | Next.js application | Set server-only `MARKETPLACE_API_URL`; avoid exposing secrets through `NEXT_PUBLIC_*`. |
| API | Express container/service | Configure Supabase credentials, CORS allow-list, proxy trust, rate limits, and reset redirect URL. |
| Database | Supabase project | Apply ordered migrations and preserve RLS/storage policy. |
| Documentation | Static MkDocs site | Publish generated `site/` using GitHub Actions. |

## GitHub Pages workflow

This repository includes `.github/workflows/deploy.yml`. A push to `main`:

1. checks out the documentation repository;
2. installs the pinned MkDocs Material dependency;
3. runs `mkdocs build --strict` so broken links/configuration fail the build;
4. uploads the generated `site/` directory as the Pages artifact;
5. deploys it to the `github-pages` environment.

In GitHub repository settings, set **Pages → Build and deployment → Source** to **GitHub Actions**. Do not select “Deploy from a branch,” because that would expose source Markdown rather than the rendered Material site.

## Before release

- Confirm the API environment contains no development URLs or placeholders.
- Confirm CORS contains the deployed web origin.
- Confirm the web gateway can reach the deployed API from its server runtime.
- Apply migrations rather than rebuilding a production database from local seeds.
- Run API tests and a web production build.
- Build docs with `--strict` and inspect representative Pages paths after deployment.
