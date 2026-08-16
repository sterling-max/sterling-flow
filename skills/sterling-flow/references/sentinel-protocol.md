# Change Risk & Sentinel Review Protocol

Protocol governing risk classification, gating rules, and review standards for Sentinel.

---

## 1. Risk Classification Matrix

| Level | Severity | Description & Surface Impact | Required Path |
| :--- | :--- | :--- | :--- |
| **`S0`** | **Trivial** | Typo fixes, comment updates, standalone documentation, non-functional text adjustments. | Fast-path (Ops or Engineer direct). |
| **`S1`** | **Low** | Isolated bug fix, single-function logic tweak, non-breaking UI style adjustment with no contract changes. | Fast-path (Engineer implements with tests). |
| **`S2`** | **Medium** | Multi-file changes, API signatures, state management alterations, dependency updates. | Full chain (Architect → Sentinel Pre-gate → Engineer → Ops). |
| **`S3`** | **Critical** | Authentication, authorization, database schemas, financial/crypto logic, persistence lifecycle, shared invariants. | Mandatory Full chain + Sentinel Post-change gate before release. |

---

## 2. Mandatory Escalation Triggers

Sentinel must automatically escalate risk level (e.g. `S1` → `S2` or `S2` → `S3`) if any of the following surfaces are touched:

1. **Security & Identity:** Session handling, token parsing, access control policies.
2. **Data Integrity:** Database migrations, cache invalidation schemes, persistence serialization formats.
3. **Core Contracts:** Public API schemas, inter-service messaging, IPC protocols.
4. **Lifecycle & State:** State machine transitions, unhandled error boundaries, race condition potential.

---

## 3. Verdict Definitions & Action Rules

### `GO`
- **Condition:** Proposal/diff is fully validated, risks are mitigated, tests are sufficient, and invariants are preserved.
- **Action:** Handoff immediately to Engineer (pre-gate) or Ops (post-gate).

### `CONDITIONAL GO`
- **Condition:** Approved *only* under specific, mandatory conditions (e.g., adding explicit edge-case tests, adding fallback handling).
- **Action:** Engineer must explicitly list and implement all required conditions before writing/finalizing code.

### `INSUFFICIENT EVIDENCE`
- **Condition:** Reproduction steps are missing, root cause diagnosis is speculative, or invariants are undocumented.
- **Action:** Halt execution. Request clarification or diagnostic proof from the Operator or Architect.

### `NO-GO`
- **Condition:** Unacceptable architectural risk, unmitigated breaking change, regression likelihood, or boundary violation.
- **Action:** Block workflow. Return directly to the Operator with detailed findings.

---

## 4. Sentinel Review Standard Format

Sentinel reviews must be concise (target ≤ 350 words) and follow this exact four-part structure:

```markdown
### Sentinel Review

**VERDICT:** [GO | CONDITIONAL GO | INSUFFICIENT EVIDENCE | NO-GO] — [Risk Level: S0-S3] — [One-line rationale]

#### Key Findings
1. **[Finding Title]** (Severity: High/Medium/Low)
   - *Evidence:* What was observed or found in the spec/diff.
   - *Consequence:* Potential failure mode if not addressed.
   - *Recommendation:* Specific required action.

#### Required Tests
- [Test 1: Focused unit/integration test case]
- [Test 2: Edge case or boundary validation]

#### Residual Risk
- [Single sentence summarizing any accepted remaining risk or monitoring requirement]
```
