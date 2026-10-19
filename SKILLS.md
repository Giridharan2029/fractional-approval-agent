# Coding-Agent Skills and Implementation Rules

## Purpose

This file is the implementation contract for coding agents working on the Fractional Approval Agent. Read it before changing code. The repository's current state and the official hackathon brief take precedence over assumptions in generated notes.

## Source-of-truth order

1. Official challenge brief and organizer clarifications (record exact links/quotes in `docs/DECISIONS.md`).
2. Actual repository code, tests, lockfiles, and reproducible command output.
3. Supplied Omega documentation in `docs/omega-reference/`.
4. Project decisions and requirements documents.
5. Prior AI-generated plans, which are proposals until verified.

Never turn an unverified assumption into a requirement. Mark it `TO VERIFY` and create a small experiment or question for the organizer.

## Non-negotiable implementation rules

- Python owns authoritative monetary calculations, input validation, policy enforcement, and authorization boundaries.
- Represent money as integer minor units (cents/paise as appropriate to the chosen currency); represent rates as integer basis points or another explicitly documented exact representation. Do not use binary floating point for settlement arithmetic.
- MeTTa/Hyperon may contribute symbolic representation and inference. Only rely on expressions demonstrated to work against the pinned, installed version.
- Omega integration must use a real, documented interface. Do not create a mock that is described as actual Omega integration.
- A missing, malformed, timed-out, errored, or inconclusive reasoning result must not authorize a payout. Fail closed and send the case to a safe hold/review state.
- No LLM or symbolic inference result may bypass deterministic financial invariants.
- Manager overrides must identify the manager, require a non-empty reason, preserve the original proposal, append an audit event, and re-run all hard validation checks. Overrides cannot make arithmetic or ownership invariants valid by assertion.
- Record provenance for inputs and rules: source, timestamp/version where available, and identifiers. Never fabricate evidence or confidence values.
- Demo transactions are simulated. Do not claim real transfers, legal compliance, or production readiness without evidence.
- Keep changes small, typed where practical, and covered by tests. Do not introduce new dependencies without documenting why and verifying installation.

## Required workflow for every task

1. Read the issue, relevant docs, and nearby code/tests.
2. State the files expected to change and any assumptions.
3. Implement the smallest coherent change within the issue scope.
4. Add or update positive, negative, boundary, and failure-path tests.
5. Run relevant tests and report the exact commands and results. If a command could not run, say why; never claim it passed.
6. Update documentation if interfaces, behavior, setup, or decisions changed.
7. Report: summary, files changed, tests run/results, limitations, and follow-up risks.

## Interface discipline

- Keep UI code separate from business logic.
- Keep financial calculations separate from MeTTa expression construction and Omega orchestration.
- Define a narrow adapter boundary for Hyperon/MeTTa and a separate adapter boundary for Omega.
- Use explicit result types/statuses such as `success`, `no_result`, `invalid_input`, `engine_error`, and `timeout`; do not overload an empty list or `None` to mean approval.
- Do not silently change public function signatures or audit schemas; update callers, tests, and docs together.

## Pull-request checklist

- [ ] Scope matches the issue.
- [ ] Financial invariants are tested, including totals and rounding/remainder allocation.
- [ ] Error and no-result cases fail closed.
- [ ] Audit records are append-only in behavior and retain before/after values.
- [ ] MeTTa/Omega behavior is backed by a reproducible test or is explicitly marked unverified.
- [ ] No secrets, private artwork data, or unsupported factual claims were added.
- [ ] Tests and setup instructions were actually run or limitations clearly stated.

## Specialized guidance

- For MeTTa implementation, follow [`docs/METTA_IMPLEMENTATION.md`](docs/METTA_IMPLEMENTATION.md).
- For Omega integration, follow [`docs/OMEGA_INTEGRATION.md`](docs/OMEGA_INTEGRATION.md).
- For agent tasks involving either technology, also follow [`skills/omega-metta/SKILL.md`](skills/omega-metta/SKILL.md).
