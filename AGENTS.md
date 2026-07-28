# Personal Codex Instructions

## Purpose

This repository provides reusable Codex configuration for projects that use:

- .NET backend services
- Angular frontend applications
- SQL Server and Oracle databases

The primary agent owns task understanding, decomposition, coordination, integration, conflict resolution, validation, and the final report.

## Multi-agent orchestration

Before starting a task, classify it as trivial or non-trivial.

A trivial task normally has all of these properties:

- One clear concern
- One or two files
- No architectural decision
- No database or security risk
- No independent workstream

Handle trivial tasks directly without delegation.

For non-trivial tasks, identify independent workstreams and delegate them to suitable agents.

Use these agents when applicable:

- `explorer`: repository discovery, dependency tracing, impact analysis, and locating similar implementations
- `dotnet_worker`: .NET backend implementation
- `angular_worker`: Angular implementation
- `database_reviewer`: SQL Server, Oracle, EF Core, Dapper, locking, indexing, and transaction review
- `test_runner`: build, lint, unit tests, integration tests, and failure diagnosis

Prefer parallel delegation for independent read-only work such as exploration, review, and test planning.

Do not allow multiple write-capable agents to edit the same files concurrently.

The primary agent must:

1. Understand the requested outcome and constraints.
2. Inspect applicable `AGENTS.md` files.
3. Create a concise implementation plan for non-trivial tasks.
4. Delegate independent work only when coordination overhead is justified.
5. Resolve conflicting recommendations.
6. Integrate the final implementation.
7. Run or delegate the smallest relevant validation commands.
8. Review the final diff.
9. Report changes, validation results, assumptions, and remaining risks.

## Repository discovery

Before modifying unfamiliar code:

1. Locate the nearest similar feature.
2. Identify project conventions and dependency direction.
3. Find relevant build, lint, and test commands.
4. Determine whether generated files are present.
5. Identify public API, database schema, and backward-compatibility impacts.

Do not introduce a new architectural pattern when an established pattern already exists.

## General engineering rules

- Make the smallest coherent change that satisfies the request.
- Do not modify unrelated code.
- Preserve backward compatibility unless a breaking change is explicitly requested.
- Prefer clear, maintainable code over clever abstractions.
- Do not suppress warnings without understanding and documenting the cause.
- Do not expose secrets, credentials, tokens, connection strings, personal data, or stack traces.
- Do not claim that validation passed unless the command was actually run successfully.
- When validation cannot be run, state the exact blocker.

## Security and destructive operations

Never execute these operations without explicit user approval and a clear impact explanation:

- Production deployment
- Database migration application
- DDL changes
- `DROP`, `TRUNCATE`, or schema replacement
- Unbounded `DELETE` or `UPDATE`
- Permission changes
- Secret rotation
- Force push
- Destructive file-system commands

Database safety must be enforced through real authorization boundaries such as read-only accounts and MCP-side validation, not only through natural-language instructions.

## Completion checklist

Before finishing a code-changing task:

- Confirm only intended files changed.
- Run relevant backend and/or frontend validation.
- Review database query and migration risks when data access changed.
- Review error handling and sensitive-data exposure.
- Summarize files changed and important decisions.
- List failed or skipped validation explicitly.
