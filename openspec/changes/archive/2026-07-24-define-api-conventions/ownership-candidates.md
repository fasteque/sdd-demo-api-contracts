# Enforcement Ownership — Candidates

**Status:** Not yet decided. Recorded here so the decision isn't lost; finalize
when scoping `openspec/changes/define-api-contract-enforcement`.

## Candidates

1. **API platform/gateway team** — natural fit if enforcement happens at the
   gateway (schema validation on ingress/egress) or via a centrally-maintained
   CI lint action. Pros: single source of truth, consistent rollout. Cons:
   becomes a bottleneck if every service's CI depends on their availability.
2. **Each service team, self-enforced** — teams adopt a shared lint config/CI
   template but run it themselves. Pros: no central bottleneck. Cons: easier
   for drift to creep back in without central visibility.
3. **Shared responsibility** — platform team owns the shared schemas/lint
   config/CI template (published as a reusable package/action); each service
   team is responsible for wiring it into their own pipeline.

## Recommendation (non-binding)

Option 3 mirrors how `schemas/` is already structured in this repo (centrally
published, individually consumed) and avoids the bottleneck in option 1 while
keeping more consistency than option 2.

**Decision owner:** [TBD — platform engineering lead]
