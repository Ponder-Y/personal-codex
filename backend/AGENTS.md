# Backend Instructions

Apply these rules to the .NET backend directory after moving this file into the actual backend project path.

## Architecture

- Follow the existing dependency direction and module boundaries.
- Keep controllers or endpoints thin; business logic belongs in application/domain services.
- Keep persistence concerns behind established data-access abstractions.
- Do not add a generic repository, mediator, or new architectural layer unless the project already uses it or the task requires it.
- Preserve public API compatibility unless a breaking change is explicitly approved.

## C# and async

- Use the C# and .NET versions configured by the project.
- Enable and respect nullable reference types.
- Use asynchronous APIs for I/O and suffix asynchronous methods with `Async`.
- Propagate `CancellationToken` through controller, service, repository, messaging, and background-job call chains.
- Do not use `.Result`, `.Wait()`, `async void`, empty catch blocks, or fire-and-forget tasks without lifecycle and exception handling.
- Prefer immutable request/response models where practical.

## API

- Do not expose EF Core entities directly.
- Use explicit request and response DTOs.
- Validate external input at the boundary.
- Use the repository's established ProblemDetails or error-envelope convention.
- Do not expose stack traces, SQL text with sensitive values, connection strings, or internal exception details.
- Use appropriate HTTP status codes and preserve idempotency semantics where applicable.

## EF Core and Dapper

- Use `AsNoTracking()` for read-only EF Core queries unless identity tracking is needed.
- Project directly into DTOs where practical.
- Avoid N+1 queries and database calls inside loops.
- Prefer `AnyAsync()` for existence checks and set-based operations for bulk changes.
- Consider transaction boundaries, optimistic concurrency, locking, retry behavior, and affected-row semantics.
- Use parameterized SQL and explicit column lists for Dapper/raw SQL.
- Verify join cardinality and nullable-column behavior.

## Logging and observability

- Use structured logging with stable property names.
- Do not log secrets, tokens, personal data, full request bodies, or unmasked identifiers.
- Preserve trace context across HTTP, messaging, and background work.
- Log enough context to diagnose failures without leaking sensitive data.

## Validation

Discover actual solution and project paths before running commands. Typical checks are:

```bash
dotnet restore
dotnet build --no-restore
dotnet test --no-build
```

Run the smallest relevant projects first. Do not claim success for commands that were not executed.
