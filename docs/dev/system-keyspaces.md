# System Keyspaces Overview

This page gives a high-level overview of several internal keyspaces and what they are used for.

## Table of Contents

- [system_replicated_keys](#system_replicated_keys)
- [system_distributed](#system_distributed)
- [system_distributed_everywhere](#system_distributed_everywhere)
- [system_auth](#system_auth)
- [system](#system)
- [system_schema](#system_schema)
- [system_traces](#system_traces)
- [system_audit/audit](#system_auditaudit)

## `system_replicated_keys`

Internal keyspace for encryption-at-rest key material used by the replicated key provider. It stores encrypted data keys so nodes can retrieve the correct key IDs when reading encrypted data.

This keyspace is created as an internal system keyspace and uses `EverywhereStrategy` so key metadata is available on every node. It is not intended for user data.

## `system_distributed`

Internal distributed metadata keyspace used for cluster-wide coordination data that is shared across nodes.

In practice, it is used for metadata such as:
- materialized view build coordination state
- CDC stream/timestamp metadata exposed to clients
- service level definitions used by workload prioritization

This keyspace is managed by Scylla and is not intended for application tables.
It is created as an internal keyspace (historically with `SimpleStrategy` and RF=3 by default).

## `system_distributed_everywhere`

Companion keyspace to `system_distributed` for metadata that must be present on every node.

It is used by internal CDC generation coordination paths and uses `EverywhereStrategy` so nodes can read this metadata locally. This keyspace is internal and not intended for user data.

## `system_auth`

Legacy auth keyspace name kept primarily for compatibility.

Auth tables have moved to the `system` keyspace (`roles`, `role_members`, `role_permissions`, and related auth state). `system_auth` may still exist for compatibility with legacy tooling/queries, but it is no longer where current auth state is primarily stored.

## `system`

See the detailed table-level documentation here: [docs/dev/system_keyspace.md](system_keyspace.md)

## `system_schema`

See the detailed table-level documentation here: [docs/dev/system_schema-keyspace.md](system_schema_keyspace.md)

## `system_traces`

Internal tracing keyspace used for query tracing and slow-query logging records (`sessions`, `events`, and related index/log tables).

This keyspace is written by Scylla's tracing subsystem for diagnostics and observability. It is operational metadata, not user application data (historically created with `SimpleStrategy` and RF=2).

## `system_audit`/`audit`

Internal audit-logging keyspace used to persist audit events when table-backed auditing is enabled.

Scylla's audit table storage is implemented as an internal audit keyspace for audit records (for example, auth/admin/DCL activity depending on audit configuration). In current code this keyspace is named `audit`, while operational material may refer to it as its historical name (`system_audit`). It is intended for security/compliance observability, not for application data.
