# ResQ360 AI Session Protocol

Use this protocol for every AI-assisted implementation session.

## Session brief template

Paste this at the start of each session:

1. Goal: [one concrete outcome]
2. Scope: [modules/files allowed]
3. Constraints: [business rules and non-negotiables]
4. Definition of done: [verification criteria]
5. Out of scope: [explicit exclusions]

## Mandatory execution rules

1. One scoped task per session block.
2. No undocumented feature implementation.
3. Confirm understanding before code changes.
4. Run verification after implementation.
5. Update docs before closing session.

## Mandatory closeout checklist

1. Delivery plan status updated.
2. Changelog entry added.
3. Decision log entry added for architecture or behavior changes.
4. Open questions captured in relevant document.

## Anti-drift guardrails

- If requirement is missing or contradictory, stop and clarify.
- If code and docs diverge, docs must be updated in same session.
- Keep diffs small and reversible.
- Prefer explicit contracts over implicit assumptions.

## Suggested prompt pattern

"Use ResQ360 docs as source of truth. Implement only [task]. Respect [constraints]. Edit only [files]. Verify with [tests/checks]. Then update [docs files]."
