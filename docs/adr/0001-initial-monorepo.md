# 0001 — Start with a monorepo

Status: Accepted

## Context

FlowShip is a learning project with coordinated changes across a small number of services and infrastructure components.

## Decision

Keep application services, infrastructure code, deployment resources, and documentation in one repository. Keep centralized runtime configuration in a separate `flowship-config` repository.

## Consequences

Local development and coordinated documentation are simpler. Each service must still be independently buildable and must not share domain entities. CI should eventually select appropriate builds without coupling all releases unnecessarily.

## Alternatives

A repository per service provides stronger repository-level separation but adds coordination overhead at this stage. Services can move later if that overhead becomes justified.
