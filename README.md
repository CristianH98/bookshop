This project is a specialized bookshop whose mission is to spread knowledge and information about the North Pole and the Arctic
The application is composed of multiple services and each one has a different purpose

Backend:
- Java
- Spring Boot, Spring Data JDBC, Spring Cloud
- PostgreSQL running as a Docker container
- Kubernetes for deployment
- Junit, Mockito and Testcontainers for tests

Frontend:
- TypeScript
- Angular



## Repository layout

This is a single repository with one directory per part of the system:

- `catalog-service` – Spring Boot service that manages the book catalog (Gradle subproject)
- `config-service` – Spring Cloud Config server (Gradle subproject)
- `config-repo` – configuration files served by `config-service`
- `polar-deployment` – Docker Compose setup for running the services locally

The Java services are subprojects of one Gradle build. Run Gradle from the repository root:

```bash
./gradlew build
./gradlew :catalog-service:bootRun
./gradlew :catalog-service:bootBuildImage
```
