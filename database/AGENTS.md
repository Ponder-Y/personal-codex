# Database Instructions

Apply these rules to database scripts, migrations, EF Core data-access code, Dapper queries, and database documentation.

## Supported databases

- SQL Server
- Oracle

Do not assume syntax, data types, date semantics, sequence behavior, identifier casing, pagination, locking hints, or transaction behavior are portable between them.

## Query rules

- Use schema-qualified object names where the project convention allows it.
- Use explicit column lists; never introduce `SELECT *` in production queries.
- Use parameterized values; never concatenate untrusted input into SQL.
- Verify join cardinality and whether joins can multiply rows.
- Handle NULL semantics explicitly.
- Keep predicates sargable where practical.
- Avoid queries inside application loops; prefer set-based operations.
- Do not recommend indexes without explaining filter/order/join support and write overhead.

## SQL Server

- Consider clustered/nonclustered index design, included columns, filtered indexes, parameter sensitivity, lock escalation, deadlocks, isolation level, and execution plans.
- Use locking hints only when the concurrency requirement is understood and documented.
- Treat `MERGE` as a concurrency-sensitive operation; review locking and correctness carefully.
- Use UTC or an explicit application timezone policy consistently.

## Oracle

- Consider sequences/identity behavior, `DATE` versus `TIMESTAMP`, empty-string-as-NULL semantics, bind variables, execution plans, and connection-pool behavior.
- Use `SELECT 1 FROM DUAL` for simple connectivity checks when appropriate.
- Do not append semicolons to SQL strings sent through providers unless the provider expects them.
- Account for Oracle identifier length and casing conventions used by the project.

## Migrations and destructive operations

Before proposing a schema change, document:

- affected objects and estimated data volume
- backward compatibility
- deployment order
- lock and downtime risk
- rollback or roll-forward strategy
- data backfill approach
- application version compatibility

Never execute DDL, DML, permission changes, or migrations without explicit approval.
Never run unbounded `DELETE` or `UPDATE`.
Never include production credentials or connection strings in repository files.

## Review output

For database findings, report:

1. Severity
2. Exact query, file, or migration
3. Failure or performance scenario
4. SQL Server and Oracle differences when relevant
5. Minimal correction
6. Required verification, such as execution plan or representative test data
