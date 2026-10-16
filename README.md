# Finance TrustGate: Fractional Approval Agent

> **Hackathon Track:** MeTTa Foundation — *The Glass Box Agent* (Built for **BASIX.Market** in the spirit of the **Omega AI Agents** neuro-symbolic architecture)
>
> A neuro-symbolic "Glass Box" compliance agent that decides whether a fractional-ownership purchase on BASIX.Market should be **approved, flagged, or rejected**—complete with an explicit confidence level, a full rule-by-rule justification trail, and a non-destructive human override that never erases the original reasoning.

---

## Problem

Fractional-ownership marketplaces like BASIX.Market face a critical compliance bottleneck: small financial decisions (e.g., *Can this buyer purchase a share of this asset?*) rely either on unscalable manual reviews or opaque "black-box" AI scoring. When an application is wrongly rejected or flagged, nobody can audit *which specific rule* triggered the outcome, leaving compliance officers and auditors with nothing defensible.

Furthermore, real-world applications frequently arrive **incomplete**—with half-finished KYC or unverified funding sources. Naive automated systems either reject every incomplete application (hurting business conversion) or silently approve them (exposing the platform to severe regulatory risk).

## Solution: The Glass Box Agent

**Finance TrustGate** replaces black-box guesswork with an inspectable, neuro-symbolic decision pipeline where **a deterministic MeTTa rule engine owns the decision** and an LLM is strictly confined to translating messy, unstructured applications into structured facts.

- **Deterministic verdicts ("Glass Box" transparency).** Same inputs, same outcome, every single time. Every reasoning step inside the MeTTa Atomspace is inspectable.
- **Explicit confidence.** Every deduction that lowers confidence is logged alongside the exact rule ID (`R1`–`R6`) that triggered it.
- **Gap handling, not gap hiding.** Partial data explicitly caps confidence and routes the application to a human compliance officer rather than silently passing or failing.
- **Non-destructive human override.** A compliance officer can grant a documented exception. The MeTTa engine re-evaluates live, and the append-only audit trail preserves both the original symbolic verdict and the human override.

## How it works

```text
Application (free text / form)
        │
        ▼
 [LLM extraction]  ── structured facts only, no decisions ──┐
        │                                                   │
        ▼                                                   ▼
 [MeTTa Atomspace]  facts + rules  ──►  verdict + confidence + rule trail
        │
        ▼
 [Python layer]  parses atoms, formats reasons, appends to audit log
        │
        ▼
 [API / UI]  "Why?" interrogation · Human override · One-page audit trail
```

**Design principle:** The symbolic MeTTa layer owns the control flow and the final verdict. The LLM acts strictly as a subordinate sensory parser, aligning with the Omega neuro-symbolic agent architecture.

## Decision rules

| ID | Rule | Effect |
| --- | --- | --- |
| R1 | Funding source not verified | **Reject** (hard stop) |
| R2 | KYC missing | **Reject** |
| R3 | KYC partial | Cap confidence at 60, **Flag** for human sign-off |
| R4 | Purchase pushes buyer above concentration limit | **Flag** |
| R5 | High-risk asset without verified KYC | **Flag** |
| R6 | All checks pass | **Approve** |

> Thresholds live in `src/rules/approval.metta` and are tunable. This table must match that file. If they diverge, the `.metta` file is the source of truth and this table is a bug.

## Demo scenarios

Planned fixtures in `data/cases.json`. Expected outcomes are **design targets** until `pytest` passes.

| Case | Situation | Target verdict |
| --- | --- | --- |
| 001 | Verified buyer, low-risk asset, low concentration | Approve |
| 002 | Partial KYC | Flag (confidence capped at 60) |
| 003 | Funding source unverified | Reject |
| 004 | Concentration over limit | Flag |
| 005 | Partial KYC, then compliance override | Flag → Approve (override logged) |

## Sample audit trail

See [`docs/AUDIT_TRAIL_SAMPLE.md`](docs/AUDIT_TRAIL_SAMPLE.md) for the one-page format. Replace the illustrative sample with real output from the running system before submitting.

## Tech stack

- **Symbolic reasoning:** MeTTa via [`hyperon`](https://github.com/trueagi-io/hyperon-experimental) (pre-alpha, version pinned in `requirements.txt`)
- **Extraction:** `<Gemini model used>`
- **Backend:** Python, FastAPI
- **Frontend:** `<Streamlit / React>`
- **Dev tooling:** Google Antigravity (see `AGENTS.md`)

## Quickstart

```bash
git clone https://github.com/<YOUR_USERNAME>/finance-trustgate.git && cd finance-trustgate
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env            # add your Gemini API key
python -m src.smoke_test        # confirms hyperon works before anything else
uvicorn src.api:app --reload
pytest
```

## Repo layout (target)

```text
finance-trustgate/
├── README.md
├── AGENTS.md              # canonical agent rules (Antigravity, Cursor, Claude Code, Codex)
├── GEMINI.md              # pointer to AGENTS.md
├── .agents/               # rules, workflows, skills for Antigravity
├── src/
│   ├── rules/approval.metta   # the symbolic decision logic
│   ├── engine.py              # hyperon bridge + atom parsing
│   ├── extract.py             # LLM extraction (structured facts only)
│   ├── audit.py               # append-only audit log
│   └── api.py
├── data/cases.json
├── tests/
└── docs/AUDIT_TRAIL_SAMPLE.md
```

## AI Disclosure (mandatory)

**Core reasoning engine:** All approve/flag/reject decisions and confidence scores are produced by deterministic MeTTa rules (`src/rules/approval.metta`). No LLM makes or influences a verdict.

**LLMs used, and where:**

| Tool / model | Used for | Not used for |
| --- | --- | --- |
| `<Gemini model>` | Extracting structured fields from free-text applications at runtime | Any decision, confidence value, or override |
| `<Antigravity / Gemini>` | Boilerplate, scaffolding, test generation, README drafting | Rule logic design (human-authored and reviewed) |
| `<Other, e.g. Claude>` | `<e.g. brainstorming, use-case analysis>` | `<...>` |

## What's next

- Real KYC provider webhook integration
- Visual rule editor so compliance teams can adjust MeTTa thresholds without touching code
- Persistent Atomspace storage across sessions
- Multi-asset portfolio concentration and cross-market risk rules on BASIX.Market

## Team

| Name | Role |
| --- | --- |
| `<Name>` | System / MeTTa lead |
| `<Name>` | Frontend & UX |
| `<Name>` | Data, docs & demo |

## License

MIT
