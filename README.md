# Polaris-Server

## Project Summary

- Polaris-Server is the central C++ orchestration and data-collection service
  for the Polaris IV&V platform. Its application entry points,
  configuration, logging, database, gRPC server, REST controllers,
  orchestration services, repositories, registry, and utilities live under
  `src/`, `internal/`, and `cmd/`. The server accepts HAL-client traffic
  over gRPC, exposes an HTTP API for dashboards and tooling, persists
  operational data in PostgreSQL, and coordinates test runs, schedules,
  heartbeats, logs, notifications, alerts, audit records, and OTA
  artifacts.

- The repository contains versioned SQL migrations from initial schema
  through runs, results, logs, OTA, audit, notifications, alerts,
  application settings, and result fields. Repository classes isolate
  persistence for targets, runs, results, logs, schedules, templates,
  tokens, users, OTA, notifications, and audit data. gRPC service
  implementations handle client and HAL interactions, while REST
  controllers expose auth, runs, modules, target, logs, settings, OTA, and
  alert routes. Docker Compose, devcontainer files, CMake presets,
  environment examples, and local setup documentation support development.

- Automated evidence includes unit tests for registry, backpressure,
  threshold/anomaly services, badges, time, result conversion, UUIDs,
  hashes, configuration, and migration integration. The CMake presets and
  README declare debug, release, test, and ASan workflows. Several
  higher-level guarantees still depend on PostgreSQL, gRPC peers,
  authentication secrets, migrations, and real OTA artifacts, so this
  README does not imply that a production deployment has been exercised.

## Feature Inventory

| # | Name / Description | Implemented? | Test Coverage? | Manual Test |
|---:|---|---|---|---|
| 1 | CMake build and executable entry point | Implemented | Configuration test and build presets | [ ] Manual verification required |
| 2 | Environment/configuration loading and logging | Implemented | `test/config_test.cpp` | [ ] Manual verification required |
| 3 | PostgreSQL client and migration runner | Implemented | `MigrationRunnerTest.cc` | [ ] Manual verification required |
| 4 | Repository persistence layer | Implemented | Indirect service/integration coverage; matrix incomplete | [ ] Manual verification required |
| 5 | HAL/client gRPC services | Implemented | Backpressure test; full wire coverage unclear | [ ] Manual verification required |
| 6 | REST API controllers | Implemented | Service tests cover selected behavior; route matrix incomplete | [ ] Manual verification required |
| 7 | Module registry and module discovery | Implemented | `ModuleRegistryTest.cc` | [ ] Manual verification required |
| 8 | Run scheduling and orchestration | Implemented | Badge/service tests; end-to-end run coverage partial | [ ] Manual verification required |
| 9 | Target heartbeats and retention scheduling | Implemented | No focused tests found | [ ] Manual verification required |
| 10 | Logs, notifications, alerts, and audit records | Implemented | Alert/audit-related tests partial | [ ] Manual verification required |
| 11 | OTA artifact storage and orchestration | Partial | No complete OTA integration test found | [ ] Manual verification required |
| 12 | API authentication, sessions, and bearer tokens | Implemented | Hash/config tests; security matrix incomplete | [ ] Manual verification required |
| 13 | Docker Compose and devcontainer workflows | Implemented | Workflow files present; container run unverified | [ ] Manual verification required |
| 14 | Debug, release, ASan, and CTest workflows | Implemented | `ctest --test-dir build/test --output-on-failure` declared | [ ] Manual verification required |

## Validation

```bash
cmake --preset debug
cmake --build build/debug
cmake --preset test
cmake --build build/test
ctest --test-dir build/test --output-on-failure
```

Provision PostgreSQL and secrets before running integration or API workflows. Verify gRPC interoperability, REST authentication, migrations, OTA, and retention manually.
