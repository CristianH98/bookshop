# Polar Bookshop

[![Catalog Service](https://github.com/CristianH98/bookshop/actions/workflows/catalog-service.yml/badge.svg)](https://github.com/CristianH98/bookshop/actions/workflows/catalog-service.yml)

Polar Bookshop is a bookshop that specializes in books about the North Pole and the Arctic. The system is a set of Spring Boot services in one repository and one Gradle build.

## What's in the repository

| Directory | Contents | Port |
|---|---|---|
| [`catalog-service`](catalog-service/README.md) | REST API for the book catalog, backed by PostgreSQL | 9001 |
| `config-service` | Spring Cloud Config server that serves configuration to the other services | 8888 |
| `config-repo` | Configuration files that `config-service` serves | |
| `polar-deployment` | Docker Compose file that runs `catalog-service` and PostgreSQL | |

`catalog-service`, `config-service`, and `config-repo` are subprojects of the Gradle build at the repository root. Run every Gradle command from the root.

## Requirements

- JDK 21 or newer. CI uses JDK 25.
- Docker. The integration tests and the local database run in containers.

## Build and test

```bash
./gradlew build
```

The integration tests start PostgreSQL 14.4 with Testcontainers, so Docker must be running.

To build or test one service, prefix the task with the project name:

```bash
./gradlew :catalog-service:test
```

## Run the catalog service locally

1. Start PostgreSQL:

   ```bash
   docker compose -f polar-deployment/docker/docker-compose.yml up -d polar-postgres
   ```

2. Start the service:

   ```bash
   ./gradlew :catalog-service:bootRun
   ```

   `bootRun` turns on the `testdata` profile, which replaces the books in the database with three sample books.

3. List the books:

   ```bash
   curl http://localhost:9001/books
   ```

The Swagger UI is at http://localhost:9001/swagger-ui/index.html.

To stop PostgreSQL and delete its data, run:

```bash
docker compose -f polar-deployment/docker/docker-compose.yml down
```

## Use the config server

`catalog-service` asks the config server at http://localhost:8888 for its configuration when it starts. If the config server is not running, `catalog-service` logs a warning and uses its local `application.yml`.

To start the config server, run:

```bash
./gradlew :config-service:bootRun
```

`config-service` reads the `config-repo` directory from the `main` branch of this repository on GitHub, not from your local copy. A change to `config-repo` takes effect only after it is merged to `main`.

## Run the catalog service in Docker

1. Build the container image:

   ```bash
   ./gradlew :catalog-service:bootBuildImage
   ```

2. Start `catalog-service` and PostgreSQL:

   ```bash
   docker compose -f polar-deployment/docker/docker-compose.yml up -d
   ```

## Tech stack

- Java 17 for `catalog-service` and Java 21 for `config-service`
- Spring Boot 4.1, Spring Cloud 2025.1, and Spring Data JDBC
- PostgreSQL 14 with Flyway migrations
- JUnit 5, Mockito, and Testcontainers
- Gradle 9.8
