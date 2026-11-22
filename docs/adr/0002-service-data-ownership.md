# 0002 — Keep service data ownership independent

Status: Accepted

## Context

User profiles, shipment lifecycle, and tracking history belong to separate business boundaries. Shared tables would couple implementations and releases.

## Decision

Each service owns its domain and database. Cross-service references use identifiers. Communication uses explicit REST contracts and, later, events. Cross-service table access, foreign keys, and shared JPA entities are prohibited.

## Consequences

Services can evolve their schemas independently. Cross-service validation needs network calls or replicated event-derived information. Distributed consistency, dependency failures, and eventual consistency require explicit design and tests.

## Alternatives

A shared database with shared entities is initially convenient but weakens service ownership and hides the distributed-system problems this project intends to explore.
