# Contributing

## Repository discipline

The API, web, and documentation projects have separate Git histories. Work in the appropriate repository; do not assume the workspace root represents a shared Git worktree. Inspect `git status` before editing or staging, preserve unrelated dirty changes, and stage explicit paths.

## Change checklist

1. Keep scope aligned to a single concern and repository.
2. When behavior changes, update the interface contract through UI/form, API validation, persistence, and display—not only one layer.
3. Add or update tests proportionate to the change.
4. Run the relevant checks from [Testing and troubleshooting](../operations/testing.md).
5. For user-facing changes, verify rendered behavior in a browser.
6. Update this guide when routes, security posture, data contracts, deployment, or runbooks change.

## Documentation standards

- Describe implemented behavior, clearly labeling a proposal or known limitation.
- Prefer internal links and concise tables over duplicated README blocks.
- Keep secrets, live access tokens, personal data, and private storage paths out of documentation and examples.
- Use Mermaid for architecture, flow, state, and entity-relationship diagrams; ensure it renders through the Material build configuration.
- Run `py -m mkdocs build --strict` before merging documentation updates.
