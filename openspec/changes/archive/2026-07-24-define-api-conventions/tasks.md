## 1. Finalize the written conventions

- [ ] 1.1 Review `specs/api-error-responses/spec.md`, `specs/api-pagination/spec.md`, and `specs/api-naming-conventions/spec.md` with at least one representative from each service team for feedback (draft ready: `review-summary.md`; awaiting actual review)
- [x] 1.2 Resolve open questions from `design.md` (cursor-based pagination, `error.details[].code` enum ownership, enforcement ownership) or explicitly defer them to a follow-up change
- [ ] 1.3 Incorporate feedback and update the spec files accordingly (blocked on 1.1)

## 2. Publish reference artifacts

- [x] 2.1 Produce a shared JSON Schema (or OpenAPI components) definition for the standard error response envelope
- [x] 2.2 Produce a shared JSON Schema (or OpenAPI components) definition for the standard pagination response envelope
- [x] 2.3 Publish both schemas to the platform's shared API artifact location so services can reference them from their own OpenAPI specs

## 3. Communicate the conventions

- [ ] 3.1 Announce the new conventions to all service-owning teams, including the migration policy (new services/endpoints immediately, existing services on next breaking-change release) (draft ready: `announcement-draft.md`; awaiting actual send)
- [x] 3.2 Add a short summary of the error, pagination, and naming/casing rules to the platform's internal API guidelines documentation

## 4. Plan enforcement follow-up

- [x] 4.1 File a follow-up change/ticket for mechanical enforcement (CI contract tests or OpenAPI/schema linting) referencing the open question in `design.md`
- [ ] 4.2 Identify and record which team owns enforcement tooling going forward (candidates recorded: `ownership-candidates.md`; awaiting decision owner)
