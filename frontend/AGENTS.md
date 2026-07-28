# Frontend Instructions

Apply these rules to the Angular frontend directory after moving this file into the actual Angular workspace path.

## Angular conventions

- Follow the Angular version and patterns already used by the project.
- Respect standalone versus NgModule architecture already present.
- Keep components focused on presentation and orchestration.
- Put API access and reusable business logic in services or established state layers.
- Prefer strict types and domain-specific interfaces; avoid `any`.
- Do not add a new state-management library unless explicitly required.

## RxJS and state

- Prefer observable composition and the async pipe where practical.
- Avoid nested subscriptions.
- Ensure long-lived subscriptions are cleaned up using the project's established pattern.
- Handle loading, empty, success, and error states explicitly.
- Avoid duplicate sources of truth and unnecessary mutable shared state.
- Use operators such as `switchMap`, `concatMap`, or `exhaustMap` according to cancellation and concurrency semantics.

## Components and templates

- Do not perform expensive calculations or API calls from templates.
- Avoid manual DOM manipulation unless an Angular abstraction is insufficient.
- Preserve accessibility, keyboard interaction, labels, and focus behavior.
- Keep templates readable; move complex conditions into typed component state.
- Reuse existing design-system and component-library conventions.

## Forms and API contracts

- Prefer typed reactive forms for complex forms when supported by the project version.
- Validate client input, while treating server validation as authoritative.
- Keep request/response types aligned with backend contracts.
- Do not silently alter date, timezone, null, undefined, or enum semantics.
- Display safe user-facing errors without leaking backend internals.

## Performance

- Use track-by or equivalent identity tracking for large collections.
- Use virtual scrolling, server-side filtering, pagination, or incremental loading for large option sets.
- Avoid unnecessary change detection and repeated subscriptions.
- Lazy-load feature routes where consistent with project architecture.

## Validation

Discover the actual package manager and scripts before running commands. Typical checks are:

```bash
npm ci
npm run lint
npm run test -- --watch=false
npm run build
```

Use `npm`, `pnpm`, or `yarn` according to the lockfile. Do not replace the package manager or regenerate lockfiles unnecessarily.
