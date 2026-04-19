# ResQ360 Documentation Hub

This folder is the single source of truth for product, engineering, and delivery decisions.

## How to use this folder

1. Do not start implementation for a new feature unless it exists in these docs.
2. Keep changes small and update docs in the same session as code changes.
3. Use the decision log for major tradeoffs and architecture changes.
4. Keep completed and pending work updated in the delivery plan.
5. Markdown files in this folder are the live source of truth.

## Structure

- `product/01-vision-and-scope.md`: Product intent, goals, non-goals, and constraints.
- `product/02-functional-specification.md`: User flows, business rules, and acceptance criteria.
- `architecture/03-technical-architecture.md`: Runtime architecture and component contracts.
- `architecture/04-data-and-api-contract.md`: Data model, enums, state rules, and API contracts.
- `delivery/05-delivery-plan.md`: Milestones, priorities, risks, and definition of done.
- `delivery/CHANGELOG.md`: Session-level change history.
- `decisions/06-decision-log.md`: Date-stamped architecture and product decisions.
- `ai-ops/07-ai-session-protocol.md`: Standard prompt protocol to avoid AI drift.

## Multi-repo placement policy

Current codebases:

- Backend repo (`resq360-backend`)
- Mobile repo (`resq360-mobile`)

Canonical documentation location:

- Keep one canonical documentation repository/folder named `project-docs` at workspace root.

If backend and mobile remain separate GitHub repositories:

1. Create a third GitHub repo named `resq360-project-docs` and push this folder there.
2. Add links to that docs repo in both backend and mobile `README.md` files.
3. Treat that docs repo as the only place where requirements and architecture are edited.

## Maintenance checklist (use every session)

1. Confirm current task exists in docs.
2. Implement one scoped task.
3. Verify behavior.
4. Update delivery plan status.
5. Add changelog entry.
6. Add decision log entry if design changed.
