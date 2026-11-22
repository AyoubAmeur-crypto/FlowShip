# Architecture overview

Status: planned architecture. Phase 0 introduces documentation only.

## Domain boundaries

| Service | Proposed fields | Ownership |
| --- | --- | --- |
| User | id, firstName, lastName, email, phone, status, createdAt, updatedAt | Business profile; no passwords |
| Shipment | id, senderId, receiverId, origin, destination, status, createdAt, updatedAt | Shipment information and valid lifecycle transitions |
| Tracking | id, shipmentId, status, location, timestamp, description | Historical shipment events |

These models are starting points, not finalized persistence schemas or API contracts. The mapping between Keycloak identities and business profiles will be defined during security implementation.

Each service will own an independent PostgreSQL database. Sharing a local PostgreSQL server may be convenient, but database ownership and access must remain separate. No service may query another service's tables. Shipment stores user identifiers; Tracking stores shipment identifiers.

## Infrastructure decisions and their triggers

| Problem | Planned component | Purpose |
| --- | --- | --- |
| Duplicated environment configuration | Config Server | Serve configuration from a separate Git repository |
| Hardcoded service addresses | Eureka | Resolve logical service names to instances |
| Multiple backend entry points | API Gateway | Route client requests through one entry point |
| Authentication and identity | Keycloak | Issue tokens and manage credentials and roles |
| Failure propagation | Resilience4j | Bound waiting and isolate failures |
| Shared rate-limit state | Redis | Coordinate limits across gateway instances |
| Asynchronous workflows | Kafka | Distribute shipment events to independent consumers |
| Distributed debugging | Observability stack | Correlate metrics, logs, and traces |
| Deployment orchestration | Kubernetes | Manage application workloads and service networking |
| Repeated Kubernetes manifests | Helm | Package and parameterize resources |

## Configuration

The planned separate repository is:

```text
flowship-config/
├── application.yml
├── api-gateway.yml
├── user-service.yml
├── shipment-service.yml
└── tracking-service.yml
```

Clients fetch configuration through Config Server. Shared configuration belongs in `application.yml`; service-specific files override it where appropriate. Secrets require a separate delivery mechanism, to be selected during implementation. Refresh behavior and behavior when Config Server is unavailable must be tested rather than assumed.

## Communication

REST provides synchronous request/response communication. OpenFeign is the Java client mechanism, and Eureka supplies service location. The first example will validate sender and receiver profiles when creating a shipment.

Kafka is deferred until shipment events need asynchronous consumers. Tracking will eventually consume these events. Event contracts must address identifiers, ordering, retries, duplicate delivery, idempotency, and eventual consistency. Reliable publication alongside database changes must be designed before treating event delivery as dependable.

## Gateway and security

Planned routes are `/api/users/**`, `/api/shipments/**`, and `/api/tracking/**`. Route rewriting and endpoint contracts remain implementation decisions.

Keycloak will support CUSTOMER, COURIER, and ADMIN roles. JWT validation at the gateway does not replace authorization in services. Customer access must also account for resource ownership; a role alone must not grant access to every customer's shipments.

## Failure handling

Calls between services require explicit timeouts. Retries must be bounded and restricted to operations safe to repeat. Circuit breakers and bulkheads will be introduced through failure experiments. A fallback must preserve business correctness: an unavailable User Service must not be interpreted as successful user validation.

## Observability

- Metrics: Actuator → Prometheus → Grafana.
- Logs: structured application logs → log collection → Loki → Grafana.
- Traces: application instrumentation with OpenTelemetry → Tempo → Grafana.

Logs should include timestamp, service name, level, trace ID, and message without exposing credentials or unnecessary personal data. Trace context should propagate across HTTP and, later, event processing. Collection and instrumentation details remain deferred.

## Deployment and testing

Services work locally first. Container work compares Dockerfiles, Buildpacks, and Jib, then adds Compose incrementally. Kubernetes follows, with native Service/DNS discovery compared against Eureka. Helm follows manually understood manifests. Hosting is deferred until the deployment architecture is stable.

Testing starts with the first business service: business unit tests, API validation and error tests, real-infrastructure integration tests, and later full user workflows. Each capability includes a reproducible failure scenario as well as its successful path.
