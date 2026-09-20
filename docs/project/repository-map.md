# Repository map

## Workspace layout

```text
marketpalce application/
├── marketplace-api/       Express API and Supabase project
├── marketplace-web/       Next.js application
├── marketplace-docs/      MkDocs engineering guide
├── start-all.ps1          Local convenience launcher
├── stop-all.ps1           Local convenience shutdown
├── dev-local.md           Local workspace notes
└── deploying.md           Deployment notes
```

## API repository

```text
marketplace-api/
├── src/
│   ├── routes/            HTTP route declarations and v1 composition
│   ├── controllers/       Request/response orchestration
│   ├── services/          Domain and Supabase operations
│   ├── middleware/        Auth, uploads, headers, rate limiting, errors
│   ├── validators/        Input validation
│   ├── config/            Supabase clients
│   ├── app.js             Express composition
│   └── server.js          Environment loading and listener
├── supabase/
│   ├── migrations/        Ordered schema, policy, function changes
│   └── seeds/             Local development data
├── tests/                 Vitest/Supertest tests
├── scripts/               Smoke, acceptance, and database audit scripts
├── Dockerfile             Production API image
└── compose.yaml           Local container wiring
```

## Web repository

```text
marketplace-web/
├── app/
│   ├── (public)/          Catalogue and information routes
│   ├── (auth)/            Login, signup, and recovery routes
│   ├── (marketplace)/     Signed-in buyer routes
│   ├── (seller)/          Role-gated seller workspace
│   ├── api/marketplace/   Same-origin API gateway
│   ├── components/        Domain-organized reusable UI
│   ├── data/, hooks/, libs/, types/
│   └── globals.css, layout.tsx
├── public/                Static assets
├── proxy.ts               Authentication and role gate
├── tests/                 UI/E2E support where present
└── vercel.json            Hosting build/install commands
```

## Documentation repository

```text
marketplace-docs/
├── docs/                  Markdown source, diagrams, JavaScript enhancement
├── .github/workflows/     GitHub Pages deployment
├── mkdocs.yml             Site configuration and navigation
└── requirements.txt       Pinned documentation dependency
```
