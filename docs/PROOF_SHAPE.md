# Shared Contract: Proof Shape & Reason Codes

This document defines the canonical JSON contract emitted by the MeTTa symbolic reasoning core and consumed by downstream components (the Audit Log, Manager Console, and Streamlit Treasury Dashboard).

---

## 1. Core Financial Principles

1. **Integer Minor Units (Zero Float):**
   - All monetary fields (`gross`, `fee`, `artist`, `community`, `dust`, `pool`, and co-owner `amount`) are represented strictly in integer minor units (e.g., USD cents: `$10,000.00` = `1000000`).
   - Floating-point representations (`float`, `decimal`, `/`, or `round()`) are strictly forbidden in runtime code, MeTTa rules, JSON exchanges, and UI rendering.
2. **Conservation Invariant:**
   - For every approved transaction, MeTTa proves the exact conservation equation:
     $$\text{ArtistRoyalty} + \text{CommunityCut} + \text{Dust} + \text{Pool} \equiv \text{Gross} - \text{Fee}$$
   - And the distributable pool exactly equals the sum of co-owner payouts:
     $$\sum_{i} \text{CoOwnerAmount}_i \equiv \text{Pool}$$
3. **Deterministic Dust Sweep:**
   - Any single-cent remainder resulting from integer division is deterministically allocated to `dust` and credited to the Community Arts Fund (`community_total = community + dust`).
4. **Fail-Closed Security:**
   - When a transaction violates covenants or validation rules, `status` is `"REJECTED"`, a non-null `reason_code` is provided, all monetary distributions are zero, `coowners` is empty (`[]`), and no funds move.

---

## 2. Canonical JSON Schema

### Approved Resale (`status: "APPROVED"`)
```json
{
  "status": "APPROVED",
  "reason_code": null,
  "artwork_id": "HFA-101",
  "currency": "USD",
  "gross": 1000000,
  "fee": 0,
  "artist": 150000,
  "community": 30000,
  "dust": 0,
  "pool": 820000,
  "coowners": [
    {
      "owner": "alice",
      "wallet": "0xalice",
      "amount": 492000
    },
    {
      "owner": "bob",
      "wallet": "0xbob",
      "amount": 328000
    }
  ],
  "source": "real-bridge-output"
}
```

### Rejected Resale (`status: "REJECTED"`)
```json
{
  "status": "REJECTED",
  "reason_code": "BELOW_RESERVE",
  "artwork_id": "HFA-101",
  "currency": "USD",
  "gross": 200000,
  "fee": 0,
  "artist": 0,
  "community": 0,
  "dust": 0,
  "pool": 0,
  "coowners": [],
  "source": "real-bridge-output"
}
```

---

## 3. Field Specifications

| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | `string` | Verdict: `"APPROVED"` or `"REJECTED"`. |
| `reason_code` | `string \| null` | `null` if approved; machine-readable reason code if rejected. |
| `artwork_id` | `string` | Unique asset identifier matching `data/artworks.json` (e.g. `"HFA-101"`). |
| `currency` | `string` | ISO currency code (e.g. `"USD"`, `"KES"`). Must match reserve currency. |
| `gross` | `integer` | Gross resale price in minor units (cents). |
| `fee` | `integer` | Platform fee deducted first (`(/ (* gross fee_bps) 10000)`). |
| `artist` | `integer` | Artist secondary royalty payout in cents (`Droit de Suite`). |
| `community` | `integer` | Base community preservation cut in cents. |
| `dust` | `integer` | Single-cent integer remainder swept to community fund (`pool - sum(coowners)`). |
| `pool` | `integer` | Distributable pool for fractional co-owners (`net - artist - community`). |
| `coowners` | `array[object]` | Ordered list of co-owner payouts: `owner` (`string`), `wallet` (`string`), and `amount` (`integer` cents). |

---

## 4. Reason Codes Reference

The symbolic engine and bridge emit standard reason codes when rejecting a proposal:

| Reason Code | Origin | Meaning |
| :--- | :--- | :--- |
| `BELOW_RESERVE` | Symbolic Rule | Proposed gross sale price is below the registered reserve price floor covenant. |
| `OVERRIDE_BELOW_FLOOR` | Symbolic Rule | A manager override attempted to set an artist royalty lower than the contractual floor covenant. |
| `OVERRIDE_NOT_ALLOWED` | Symbolic Rule | A manager override attempted to reduce the community fund allocation. |
| `ARTWORK_CONTESTED` | Symbolic Rule | Artwork status is marked `CONTESTED` (e.g., active legal dispute or probate claim). |
| `UNSUPPORTED_CURRENCY` | Symbolic Rule | Settlement currency does not match the artwork's registered reserve currency. |
| `DUPLICATE_NONCE` | Gateway / Audit | Replay attack detected; transaction nonce has already been settled. |
| `FAILED_CLOSED` | Engine Boundary | MeTTa engine returned empty atom set, unmapped entity, or syntax error; execution halted safely. |
| `INVALID_AMOUNT` | Input Validation | Gross amount is non-integer, negative, zero, or contains forbidden float/boolean types. |
| `AMOUNT_ABOVE_CAP` | Policy Rule | Proposed gross exceeds maximum supported transaction cap ($10,000,000.00 / 1,000,000,000 cents). |
| `CAP_TABLE_INVALID` | Policy Rule | Cap table shares do not sum to 10,000 bps or deductions exceed 10,000 bps. |

---

## 5. Test Fixtures Reference
Canonical proof fixtures generated directly by `agent/bridge.py` are stored in:
`tests/fixtures/proofs/`
- `sc01_standard_resale.json`: Standard happy path secondary resale on `HFA-101`.
- `sc02_below_reserve.json`: Rejection of $2,000 bid against $3,000 reserve (`BELOW_RESERVE`).
- `sc03_prompt_injection.json`: Prompt injection resistance on `HFA-102` (stored 1200 bps rule enforced).
- `sc04_manager_override.json`: Valid upward manager override on `HFA-103` (+200 bps).
- `sc05_override_below_floor.json`: Rejection of override below 1000 bps floor (`OVERRIDE_BELOW_FLOOR`).
- `sc06_dust_split.json`: Single-cent dust remainder sweep to community on `TEST-DUST`.
- `sc07_duplicate_nonce.json`: Idempotency replay defense (`DUPLICATE_NONCE`).
- `sc08_metta_unavailable.json`: Engine error / unmapped entity fail-closed (`FAILED_CLOSED`).
- `sc09_llm_unavailable.json`: Offline rule-based execution fallback.
