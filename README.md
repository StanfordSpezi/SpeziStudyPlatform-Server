<!--

This source file is part of the Stanford Spezi open source project

SPDX-FileCopyrightText: 2026 Stanford University and the project authors (see CONTRIBUTORS.md)

SPDX-License-Identifier: MIT

-->

# SpeziStudyPlatform-Server

[![CI](https://github.com/StanfordSpezi/SpeziStudyPlatform-Server/actions/workflows/ci.yml/badge.svg)](https://github.com/StanfordSpezi/SpeziStudyPlatform-Server/actions/workflows/ci.yml)
[![codecov](https://codecov.io/gh/StanfordSpezi/SpeziStudyPlatform-Server/branch/main/graph/badge.svg?token=X7BQYSUKOH)](https://codecov.io/gh/StanfordSpezi/SpeziStudyPlatform-Server)

A Vapor-based server for managing clinical research studies, built as part of the Spezi ecosystem.

## Overview

Spezi Study Server provides a REST API for managing clinical research studies, including:

- **Studies** - Create and manage study definitions with metadata
- **Components** - Configure study components (questionnaires, health data collection, informational content)
- **Schedules** - Define when components should be presented to participants

The server integrates with [SpeziStudy](https://github.com/StanfordSpezi/SpeziStudy) for study configuration types and [SpeziVapor](https://github.com/StanfordSpezi/SpeziVapor) for dependency injection.

## Requirements

- Swift 6.0+
- Docker

## Local Development Setup

The server requires PostgreSQL and Keycloak. See [SpeziStudyPlatform-Infrastructure](https://github.com/StanfordSpezi/SpeziStudyPlatform-Infrastructure) for setting up these services. Run `swift run SpeziStudyPlatformServer migrate --yes` before the first startup to run the database migrations.

### Run Tests

```bash
swift test --no-parallel
```

Tests must run without parallelism (`--no-parallel`) because integration test suites share an in-memory SQLite database per suite. Tests use mock JWT signing — no Docker services required.

### Docker Compose Services

| Service   | Description                                               |
| --------- | --------------------------------------------------------- |
| `app`     | Production server (requires `docker compose build` first) |
| `migrate` | Run migrations manually (`docker compose run migrate`)    |
| `revert`  | Revert migrations (`docker compose run revert`)           |

Infrastructure services (PostgreSQL, Keycloak) are defined in [SpeziStudyPlatform-Infrastructure](https://github.com/StanfordSpezi/SpeziStudyPlatform-Infrastructure).

### Bruno

API request collections are available in `tools/bruno/`. [Bruno](https://www.usebruno.com) is an open-source API client. Select the **SpeziStudy** environment in Bruno to load the required variables.

Run the **Seed** request to bootstrap a working dataset, it logs in via Keycloak, fetches groups, creates a study, and adds sample components in sequence. Requests use post-response scripts to pass IDs (e.g. `groupId`, `studyId`) to subsequent requests automatically.

## API Documentation

The API is defined using OpenAPI. The specification and generated types live in the shared [SpeziStudyPlatform-API](https://github.com/StanfordSpezi/SpeziStudyPlatform-API) package.

## Architecture

The server follows a module-based architecture:

```
Sources/SpeziStudyPlatformServer/
├── App/              # Application bootstrap and configuration
├── Modules/          # Feature modules (Study, Component, etc.)
├── Models/           # Fluent database models
├── Migrations/       # Database migrations
└── Core/             # Shared infrastructure
```

Each module contains its own Controller, Service, Repository, and Mapper.

## License

This project is licensed under the MIT License. See [Licenses](https://github.com/StanfordSpezi/SpeziStudyPlatform-Server/tree/main/LICENSES) for more information.

## Contributors

This project is developed as part of the Stanford Mussallem Center for Biodesign at Stanford University.
See [CONTRIBUTORS.md](https://github.com/StanfordSpezi/SpeziStudyPlatform-Server/tree/main/CONTRIBUTORS.md) for a full list of all Spezi Study Server contributors.

![Stanford Mussallem Center for Biodesign Logo](https://raw.githubusercontent.com/StanfordBDHG/.github/main/assets/biodesign-footer-light.png#gh-light-mode-only)
![Stanford Mussallem Center for Biodesign Logo](https://raw.githubusercontent.com/StanfordBDHG/.github/main/assets/biodesign-footer-dark.png#gh-dark-mode-only)
