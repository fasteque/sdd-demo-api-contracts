# sdd-demo-api-contracts

An **OpenSpec store** — a standalone planning repository holding platform-wide
API conventions, consumed by multiple independent microservices. There is no
application code here. This repo exists to test whether Spec-Driven Development
scales beyond a single service/repo, which a single-repo PoC cannot demonstrate
on its own.

## What a "store" is

OpenSpec normally keeps specs inside the same repo as the code they describe.
A **store** (beta feature) is just a separate repo with the same `openspec/`
shape, that other repos can reference read-only:

```yaml
# in a consuming repo's openspec/config.yaml
references:
  - { id: sdd-demo-api-contracts, remote: "https://github.com/fasteque/sdd-demo-api-contracts.git" }
```

Once referenced, an AI coding agent working in the consuming repo can look up
what this store defines and apply it — including deciding on its own that a
change is significant enough to require doing so, without being told exactly
where to look. See [`sdd-demo`](https://github.com/fasteque/sdd-demo)'s
`CLAUDE.md` and README for that in practice.

## What's actually in here

- **`openspec/specs/`** — the platform-wide conventions, as living, versioned
  specs:
    - `api-error-responses` — standard JSON error envelope for all 4xx/5xx
      responses (`error.code`, `error.message`, `error.traceId`,
      `error.details`)
    - `api-pagination` — standard page-based pagination shape
      (`data` + `pagination` envelope)
    - `api-naming-conventions` — casing/naming rules across JSON fields, URL
      paths, query params, headers, and enum values
- **`schemas/`** — the actual reusable JSON Schema files for the above, meant
  to be referenced (`$ref`) from each consuming service's own OpenAPI spec
  rather than redefined per-service
- **`openspec/changes/`** — the proposal history for how these conventions were
  defined and reasoned about, including deliberately deferred follow-up work
  (e.g. mechanical enforcement/linting — filed as a stub, intentionally not
  scoped yet)

## What does NOT belong in this repo

- No application code, no build tooling — this repo has nothing to compile or run
- No service-specific detail (a rule that only makes sense for one service
  belongs in that service's own `openspec/specs/`, not here)
- No Compound Engineering plugin — there's no code to plan, implement, or
  review; see this repo's `CLAUDE.md` for the explicit scope boundary

## Consuming services

- [`sdd-demo`](https://github.com/fasteque/sdd-demo) — Asset Catalog API,
  references this store
- Product Management service *(planned)* — will reference this store from day
  one, to confirm the pattern holds for a second, independent consumer, not
  just the one it was originally built around

## Workflow

Standard OpenSpec only, no Compound Engineering: `/opsx:propose` → human review
→ `/opsx:apply` → `/opsx:sync` → `/opsx:archive`. Since there's no code, `apply`
typically produces documentation and shared schema files rather than
implementation. Tasks requiring real-world coordination (team review, rollout
announcements, ownership decisions) are deliberately left unchecked in
`tasks.md` until that coordination genuinely happens — never marked done as a
placeholder.

## Known limitations

- OpenSpec's Stores feature is explicitly beta; commands and behavior may
  change between releases
- Nothing here is mechanically enforced yet (no CI linting, no schema
  validation against consuming services) — enforcement is a deliberately
  deferred follow-up, tracked as `define-api-contract-enforcement`
- This is a personal evaluation exercise, not an officially adopted standard

## Status

Active. One convention set defined and archived (`define-api-conventions`).