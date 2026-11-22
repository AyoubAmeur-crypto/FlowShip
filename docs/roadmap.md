# Development roadmap

Only documentation and the starter directory structure are complete. All runtime capabilities below are planned.

## Phase 0 — Repository initialization

- [x] Add project README and `.gitignore`.
- [x] Add starter folders, architecture documentation, and initial ADRs.
- [ ] Initialize and connect the FlowShip Git repository (owner-managed).
- [ ] Create and connect the separate FlowShip Config repository (owner-managed).
- [ ] Select a license before public distribution.

## Phase 1 — Centralized configuration

Create Config Server, connect it to the GitHub configuration repository, add shared and client-specific configuration, and prove a tiny client retrieves a remote value. Test refresh behavior, startup dependency failures, and configuration versus secrets.

Done when a Spring Boot client demonstrably loads Git-backed configuration through Config Server and the verification steps are documented.

## Phase 2 — Service discovery

Create Eureka Server, register the first client, inspect registration, and compare logical service names with hardcoded addresses.

Done when a registered service is visible in Eureka and registration behavior is understood.

## Phase 3 — User Service

Implement a simple profile API, PostgreSQL persistence, validation, exception handling, tests, Config Server integration, Eureka registration, and Actuator.

Done when profile behavior and invalid requests are tested against the API and persistence boundary.

## Phase 4 — API Gateway

Configure discovery-backed routes, filters, correlation propagation, and failure-case tests.

Done when requests reach User Service through the gateway and unavailable destinations produce understood behavior.

## Phase 5 — Security

Run Keycloak; define a realm, client, and roles; configure JWT validation and service-level authorization. Test CUSTOMER and ADMIN access, resource ownership, unauthenticated requests, and forbidden operations.

Done when both gateway and business service enforce their intended security boundaries.

## Phase 6 — Business services

Implement Shipment and Tracking services with independent PostgreSQL databases, lifecycle rules, unit tests, API tests, and integration tests.

Done when each service owns and verifies its business behavior independently.

## Phase 7 — Service communication

Introduce REST/OpenFeign user validation through Eureka. Define dependency error mapping and timeouts.

Done when sender/receiver validation works and missing users and unavailable dependencies have distinct, tested outcomes.

## Phase 8 — Resilience

Simulate failures and experiment with timeouts, bounded retries, circuit breakers, bulkheads, and correctness-preserving fallbacks.

Done when failure propagation and recovery behavior are reproducible and documented.

## Phase 9 — Redis

Introduce Redis only for a demonstrated need, initially distributed rate limiting; consider caching later.

Done when the chosen use case works across instances and Redis failure behavior is tested.

## Phase 10 — Kafka

Publish and consume shipment events. Explore topics, partitions, offsets, consumer groups, ordering, retries, dead-letter handling, idempotency, duplicate delivery, and eventual consistency.

Done when Tracking consumes shipment events reliably under the documented delivery contract and replaying an event does not duplicate its business effect.

## Phase 11 — Observability

Add Actuator, Prometheus, and Grafana for metrics; structured logging and Loki for logs; OpenTelemetry and Tempo for traces. Verify trace IDs and propagation across services.

Done when a distributed operation can be investigated using correlated telemetry.

## Phase 12 — Container images

Compare Dockerfiles, Spring Boot Buildpacks, and Google Jib for image size, build speed, reproducibility, developer experience, and CI integration.

Done when the comparison uses repeatable builds and documented measurements.

## Phase 13 — Docker Compose

Incrementally assemble services, databases, Keycloak, Kafka, Redis, and observability with health checks and documented setup.

Done when the implemented system can be started reproducibly and its health verified.

## Phase 14 — Kubernetes

Learn namespaces, Deployments, Services, ConfigMaps, Secrets, Ingress, persistent storage, readiness/liveness probes, resource limits, scaling, and workload inspection. Compare Service/DNS discovery with Eureka.

Done when workloads can be deployed, inspected, and exercised through failure and recovery scenarios.

## Phase 15 — Helm

Convert understood manifests into a chart with environment-specific values. Practice install, upgrade, and rollback.

Done when the chart reproduces the intended deployment and rollback is verified.

## Phase 16 — CI/CD

Use GitHub Actions to compile, run unit and integration tests, package applications, build and publish images, deploy, and run smoke tests. Choose hosting and environment promotion details when deployment requirements are known.

Done when a change can pass the documented pipeline into the selected environment with verified smoke tests.

## Completion rule

For each phase, document the business or engineering problem, decision, implementation, verification, observed failure behavior, and the next justified capability. A successful compilation alone does not complete a phase.
