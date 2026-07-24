# Project Conventions — sdd-demo-api-contracts

## What this repo is

This is an **OpenSpec store** — a standalone planning repository, not a code repo.
It holds platform-wide API conventions consumed by multiple independent microservices
(currently `sdd-demo`, `sdd-demo-products`, and future services).

There is no application code here. There is nothing to build, test, or run.

## What does NOT apply here

- No Compound Engineering plugin — there is no code to plan, implement, or review
- No tiers, no `ce-*` commands, no dependency whitelist
- No OpenAPI generation workflow — this repo does not implement any service's API,
  it only defines conventions those services should follow

## Workflow

Standard OpenSpec only: `/opsx:propose` → human review → `/opsx:apply` → `/opsx:archive`.
Since there is no code, `/opsx:apply` typically only produces documentation and shared
schema artifacts under `schemas/`, not application code.

## Rules

- Nothing defined here may reference a specific consuming service's field names,
  endpoints, or domain model — conventions must generalize across all services
- Tasks that require real-world coordination (team review, announcements, ownership
  decisions) must be left unchecked in `tasks.md` until that coordination genuinely
  happens — never marked done as a placeholder
