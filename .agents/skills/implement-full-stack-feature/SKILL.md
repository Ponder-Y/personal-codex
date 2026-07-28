---
name: implement-full-stack-feature
description: Implement a non-trivial feature spanning a .NET backend, Angular frontend, and optionally SQL Server or Oracle data access, with multi-agent exploration, implementation, database review, and validation.
---

# Implement Full-stack Feature

Use this skill when a requested feature crosses multiple technical layers or requires coordinated exploration, implementation, database review, and validation.

Do not use this skill for simple explanations, typo fixes, or isolated one-line changes.

## Inputs to resolve from repository context

Determine these from the task and codebase before implementation:

- expected user behavior
- affected backend and frontend modules
- API request and response contract
- SQL Server, Oracle, or database-independent data source
- authorization and validation requirements
- backward-compatibility constraints
- relevant build and test commands

Use reasonable assumptions only when they are low risk. State material assumptions in the final report.

## Workflow

### 1. Explore

Delegate read-only discovery to `explorer`.

Request:

- nearest similar feature
- affected files and symbols
- current API and data flow
- established architecture and test patterns
- generated files or contracts that must not be edited manually
- likely risks

### 2. Plan

The primary agent creates a concise plan and divides work by ownership.

Recommended ownership:

- `.NET` implementation → `dotnet_worker`
- Angular implementation → `angular_worker`
- SQL/EF Core/Dapper review → `database_reviewer`
- Validation → `test_runner`

Run independent read-only work in parallel. Avoid concurrent writes to the same files or generated artifacts.

### 3. Define contract

Before parallel backend/frontend implementation, establish:

- endpoint and HTTP method
- request fields and validation
- response fields and nullability
- enum/date/timezone semantics
- error/status behavior
- authorization requirements

Do not let frontend and backend agents invent conflicting contracts.

### 4. Implement backend

Delegate backend scope to `dotnet_worker` with exact ownership boundaries.

Require:

- existing architecture and naming conventions
- DTO boundaries
- async and CancellationToken propagation
- validation and safe error handling
- parameterized data access
- focused tests for behavior changes

### 5. Implement frontend

Delegate frontend scope to `angular_worker` with the agreed API contract.

Require:

- strict typing
- loading, empty, success, and error states
- accessible interaction
- correct RxJS cancellation/concurrency semantics
- focused tests where the project supports them

### 6. Review database impact

Use `database_reviewer` whenever the change affects queries, EF Core mappings, Dapper SQL, migrations, transactions, indexes, or data semantics.

The reviewer must not execute write operations. Resolve high-severity correctness, security, data-loss, locking, or performance findings before completion.

### 7. Integrate

The primary agent:

1. Integrates agent outputs.
2. Resolves contract or design conflicts.
3. Checks that generated files were handled correctly.
4. Reviews the complete diff for unrelated changes.
5. Verifies sensitive data is not exposed.

### 8. Validate

Delegate or run the smallest relevant validation commands.

Potential backend checks:

```bash
dotnet restore
dotnet build --no-restore
dotnet test --no-build
```

Potential frontend checks:

```bash
npm ci
npm run lint
npm run test -- --watch=false
npm run build
```

Use the repository's actual commands and package manager. Do not claim success unless the command completed successfully.

## Completion report

Return:

- summary of implemented behavior
- agents used and their responsibilities
- files changed
- API or database contract decisions
- validation commands and results
- skipped validation and exact blockers
- remaining risks or follow-up work
