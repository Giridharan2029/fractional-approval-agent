---
name: omega-metta-implementation
description: Project-specific instructions for implementing and testing Hyperon/MeTTa rules and integrating the actual Omega framework in the Fractional Approval Agent.
---

# Skill: Omega + MeTTa Implementation

Before editing this project, read `SKILLS.md`, `docs/METTA_IMPLEMENTATION.md`, and `docs/OMEGA_INTEGRATION.md`. Consult `docs/omega-reference/` for source guidance and verify all relevant behavior in the local environment.

## Operating rules

1. Do not infer API behavior from a filename or conceptual description. Locate the relevant supplied reference and run a minimal test.
2. Do not assume the full Omega daemon is required or unnecessary. Follow the recorded integration decision and spike evidence.
3. Keep financial arithmetic, hard invariants, and final authorization in deterministic Python.
4. Use MeTTa for narrowly scoped, explicitly grounded symbolic rules. Treat no result, contradiction, malformed expression, timeout, and runtime error as non-approval/review states.
5. Keep Omega-specific calls behind an adapter. Do not call a Python-only orchestrator “Omega integration.”
6. Do not invent truth values, evidence, provenance, supported rule patterns, or test results.
7. Do not inject raw user input into executable MeTTa expressions. Parse and validate inputs and construct atoms safely.
8. Never allow self-modifying code or rules to become active without tests, review, versioning, and explicit approval.
9. For every change, add tests for expected behavior and failure paths; report exact commands and outcomes.

## Required completion report

- What changed and why.
- Which source docs were consulted.
- Which MeTTa/Omega behavior was actually executed and verified.
- Exact test commands and results.
- Unverified assumptions, known limitations, and next steps.
