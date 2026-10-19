# MeTTa / Hyperon Implementation Guide

**Status:** Implementation guidance; runtime and expressions remain unverified until executed in the project environment.

## Goal and boundary

Use Hyperon/MeTTa for a small, explicit symbolic layer: representing approved facts and relationships, evaluating selected rules, and returning machine-readable conclusions. Python remains authoritative for financial arithmetic, input validation, deterministic hard constraints, final authorization, and audit persistence.

Do not treat MeTTa as a trusted calculator or as an authorization boundary. A symbolic conclusion is evidence for the workflow, not permission to transfer money.

## Start with a minimal runtime probe

Before building domain rules:

1. Confirm the supported Python version and the current official Hyperon installation instructions.
2. Create an isolated environment and pin the exact dependency version that successfully installs.
3. Run a minimal import/runtime smoke test using the documented API for that version.
4. Run one tiny MeTTa expression and capture the exact command and output.
5. Add the probe to automated tests or a documented manual verification script.
6. Record OS, Python version, package version, install command, test command, and output in `docs/DECISIONS.md` or a test report.

Do not invent a Python API or assume a package version from an old tutorial. If install or import fails, record the failure and resolve it before domain logic depends on the engine.

## Domain model: keep atoms simple and grounded

Start with a small vocabulary, documented in this file or a linked schema. Candidate concepts include:

- artwork identifier;
- creator identifier;
- ownership record and owner identifier;
- transaction identifier;
- versioned policy identifier;
- evidence reference;
- proposed decision and reason code.

Choose relation direction and MeTTa representation deliberately. Avoid representing a fact only as a free-form sentence. Every atom used for a decision should map to a validated input or a versioned policy. Do not put names, percentages, ownership shares, or factual assertions into the knowledge base unless the source is recorded.

## Rule design

- Begin with one or two small rules, each tied to an explicit acceptance test.
- Separate factual premises from policy rules.
- Prefer bounded, short inference paths over long chains.
- Make missing premises observable. A missing conclusion is `no_result`, not `approve`.
- Do not assign truth values based on intuition. If the selected engine uses frequency/confidence values, document their meaning and source and test how they are consumed.
- Never use a truth value as a substitute for deterministic validation of sums, ownership shares, or currency arithmetic.
- Do not assume all NAL/PLN examples in the Omega docs are functional in this version. Reproduce the exact example first, then adapt it in a separate test.

## Suggested first tests

1. A known, grounded premise produces the expected conclusion.
2. Removing a required premise results in no conclusion or a safe non-approval status.
3. Reversing an asymmetric relation does not accidentally pass.
4. Contradictory premises are surfaced rather than silently resolved in favor of approval.
5. Malformed expressions and runtime errors become typed engine failures.
6. Repeated runs on the same fixed facts produce the expected stable result.
7. Untrusted user text cannot inject executable MeTTa expressions into the evaluator.

## Python adapter contract

Wrap the engine behind a narrow adapter rather than scattering runtime calls across the app. The adapter should return a typed result containing:

- status (`success`, `no_result`, `invalid_input`, `engine_error`, `timeout`);
- normalized conclusions, if any;
- engine/package version;
- rule-set version or commit;
- input fact/evidence identifiers;
- diagnostic details safe for logs.

Do not expose raw exceptions to the UI or convert exceptions into successful empty results. Never allow a raw user-supplied string to be concatenated into executable expressions without a safe parser/escaping strategy.

## Change management

Every rule change must include tests and a rule-set version change or commit reference in audit events. If adaptive or self-modifying behavior is explored, treat generated changes as proposals: sandbox, validate, test, review, version, and explicitly activate them. No runtime self-modification may silently alter live payout or approval rules.

## Source documentation

Use the supplied files in [`omega-reference/`](omega-reference/), especially:

- `tutorial-05-reasoning-with-nal-pln.md` for the documented worked examples;
- `tutorial-07-grounded-reasoning.md` for grounding;
- `tutorial-08-reliable-reasoning.md` for reliability guidance;
- `reference-skills-reasoning.md` for Omega's documented `(metta ...)` skill surface;
- `reference-lib-nal.md` and `reference-lib-pln.md` for documented engine limits;
- `reference-failure-modes.md` for the source's failure taxonomy.

These are supplied documentation claims, not proof that the examples work in our pinned environment. Verify before depending on them.
