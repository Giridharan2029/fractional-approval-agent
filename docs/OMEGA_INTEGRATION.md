# Omega Integration Guide

**Status:** Decision pending a minimal local experiment and, if possible, organizer clarification.

## Objective

Determine the smallest genuine integration with SingularityNET's Omega framework that satisfies the hackathon's stated stack requirement and is reliable within the project deadline. The challenge slide names MeTTa, Hyperon, and Omega; it does not by itself specify the minimum acceptable Omega deployment. Do not claim that an independently written Python loop is actual Omega integration.

## What the supplied Omega docs say

The supplied documentation describes:

- a MeTTa skill catalogue and agent loop;
- a documented `metta` skill whose signature is `(metta sexpression)` and whose result depends on the inner expression;
- NAL and PLN reasoning libraries and orchestration guidance;
- optional Python/Prolog bridges and extension points for custom skills;
- memory and channel functionality, much of which may be irrelevant to this transaction-focused demo;
- ONA as experimental/planned and not installed by default in the supplied reference.

Relevant references are `introduction.md`, `reference-skills-reasoning.md`, `reference-orchestration.md`, `reference-internals-skill-dispatch.md`, `reference-internals-extension-points.md`, `reference-python-bridges.md`, `reference-configuration.md`, and `tutorial-03-writing-a-custom-skill.md` in `docs/omega-reference/`.

The docs' description of an interface is not proof that the current checkout, operating system, or dependency set supports it unchanged. Verify against the actual repository revision and runtime.

## Integration options to evaluate

### Option A — Use an existing Omega skill/interface

Preferred if a stable, documented interface can be invoked without deploying unrelated optional infrastructure. Verify its input/output contract, error behavior, and startup requirements.

### Option B — Add a custom Omega skill

Use only if the existing interface is insufficient and the task justifies modifying Omega. The supplied tutorial says a skill typically requires an entry in `src/skills.metta`, a MeTTa definition, and optional Python/Prolog glue. This may mean maintaining a fork or patch; assess that cost before choosing it.

### Option C — Standalone Python orchestration plus Hyperon/MeTTa

Keep as a fallback if actual Omega integration is infeasible. Describe it accurately as a standalone orchestrator using Hyperon/MeTTa, and ask the organizers whether this meets the stack requirement. Do not label it as a completed Omega integration without evidence.

## Minimal spike checklist

Create a short-lived branch and answer these questions with evidence:

1. Which Omega repository revision and documentation revision are being used?
2. What are the documented OS/runtime/install prerequisites?
3. Can the project run the smallest official smoke test?
4. Can a host application invoke the relevant Omega/MeTTa skill through a supported interface?
5. What does success, no-result, malformed input, timeout, and engine error look like?
6. Can we capture the result and relevant metadata without depending on the chat transcript as an audit store?
7. What extra services are actually required for this narrow path (for example, memory store, channels, Prolog, containers)? Verify rather than assume.
8. Is the integration lightweight enough to install from a clean environment and reproduce during judging?

Deliver a short report: commands run, versions, outputs, blockers, estimated integration effort, and recommendation. Do not build the rest of the app around unverified assumptions.

## Application boundary

The application's Python workflow should call a narrow `OmegaAdapter` interface. Keep Omega-specific imports and process/protocol details inside that adapter. Suggested normalized statuses are `success`, `no_result`, `invalid_input`, `engine_error`, and `timeout`.

The adapter must not be allowed to:

- calculate authoritative payout amounts;
- directly transfer funds or perform irreversible actions;
- override deterministic ownership, sum, currency, or policy invariants;
- silently interpret missing output as approval.

If Omega is unavailable or returns an unexpected result, put the transaction on hold and record a diagnostic audit event.

## Audit and provenance

Record, where available:

- transaction and proposal IDs;
- input/evidence references and hashes or stable IDs;
- Omega repository/version and adapter version;
- Hyperon/MeTTa package version and rule-set commit;
- normalized reasoning result and status;
- deterministic policy results;
- manager identity, reason, timestamp, and before/after decisions;
- final simulated authorization status.

Do not store secrets or unnecessary personal data. The audit log should preserve original and override events instead of rewriting history.

## Decision gate

Do not finalize the Omega architecture until the spike is complete. Record one of:

- `VERIFIED`: real Omega interface exercised successfully, with reproducible commands;
- `BLOCKED`: actual integration attempted and blocker documented;
- `NOT REQUIRED — ORGANIZER CONFIRMED`: written organizer confirmation is linked or quoted;
- `FALLBACK`: standalone Python + Hyperon/MeTTa selected with the limitation explicitly disclosed.

## Suggested issue acceptance criteria

- A clean-checkout procedure is documented.
- The selected interface is real and versioned, or the fallback is explicitly labeled.
- Success and failure outputs are tested.
- No engine error or empty result can approve a transaction.
- No unsupported claim about full Omega deployment or compliance appears in the README/demo.
