---
name: sterling-flow
description: Multi-agent orchestration skill for OpenAI Codex across Sterling Lab projects. Routes tasks across Architect, Sentinel, Engineer, Ops, and Helper agents with strict risk gating, Operator authority, and model-tier token optimization.
---

# Sterling Flow Orchestration

Orchestrates multi-agent development workflows across OpenAI Codex environments (CLI, IDEs, desktop, and agent platforms), balancing rigorous quality gating with token economics.

---

## 1. Core Operating Principles

1. **Direct Subagent Access:** The Operator can directly consult, prompt, or chat with any individual subagent (e.g., brainstorming with Architect, spot-checking with Sentinel) without triggering an automated chain.
2. **Operator Absolute Authority:** Subagents cannot trigger irreversible actions. Commits, pushes, deployments, and tag releases are **only** executed when explicitly ordered by the Operator. S2 and S3 implementation must not begin until the Operator explicitly approves the Sentinel pre-gate verdict; S3 release must not proceed until the Operator explicitly approves the post-gate verdict.
3. **Missing Data Escalation:** Subagents must **never guess or fabricate missing context** (e.g. repo names, deploy targets, version numbers, API keys). If required data is missing from project docs or environment, the subagent must immediately halt and prompt the Operator for clarification.
4. **Token Optimization & Verbosity Control:**
   - **No Raw Diffs in Output:** The IDE handles visual diffing. Agents never dump full code diffs into chat context.
   - **Inter-Agent Protocol:** Strictly factual and minimal; agents only pass necessary parameters, constraints, and contracts.
   - **Operator Output:** Concise executive summaries (files changed, test results, next decision). Target ≤ 100 words.
5. **Smallest Effective Flow:** Do not spawn a chain for every request. Use direct execution for S0/S1 work; reserve Architect and Sentinel for S2/S3 or explicit Operator requests.
6. **Task Drift Control:** Every delegated task must preserve the original objective, scope, constraints, and completion criteria. If those change materially, stop and return to the Operator for approval.
7. **Capability-Based Delegation:** The coordinating/default agent must delegate to the lowest-cost role that can complete the next step. Do not implement work locally merely because the coordinator has write access.

---

## 2. Model Tier & Effort Matrix

| Tier | Codex Model | Default Effort | Speed | Primary Roles |
| :--- | :--- | :--- | :--- | :--- |
| **Sol** | `gpt-5.6-sol` | `medium` / `high` | Normal | **Engineer** (S2/S3 complex implementation & refactoring) |
| **Terra** | `gpt-5.6-terra` | `medium` | Fast / Normal | **Architect** (Specs), **Sentinel** (Risk Gate), **Ops** (Release), **Engineer** (S0/S1) |
| **Luna** | `gpt-5.6-luna` | `low` | Fast | **Helper** (Leaf text formatting, summaries, bullet extraction) |

> [!TIP]
> Use the lowest reasoning effort that reliably meets the task’s quality bar. `medium` is the default balance; use `high` for difficult implementation or review. Increase effort only when representative tasks show a measurable quality benefit. See the [official GPT-5.6 model guidance](https://developers.openai.com/api/docs/guides/latest-model).

---

## 3. Default Solo Flow

For a solo developer, Sterling Flow should feel like one assistant with lightweight internal checkpoints:

1. State the task brief: **objective, scope, constraints, and done when**.
2. Classify the work as S0–S3.
3. Execute directly for S0/S1; use the full chain only for S2/S3.
4. Before finishing, report changed files, validation, remaining risk, and the next decision.

The Operator can request any individual role directly when useful. Roles are optional tools, not mandatory ceremony.

### Default execution protocol

`Understand → classify → delegate by capability → execute → validate → summarize`

- Pure text transformation or formatting: delegate to **Helper (Luna)**; the parent agent applies the result.
- Low-risk code or documentation requiring repository edits: **Engineer (Terra)**.
- Feature framing or independent review: **Architect/Sentinel (Terra)**.
- Complex implementation: **Engineer (Sol)**.
- Commit, push, or deployment: **Ops (Terra)** only after explicit Operator instruction.

The coordinator should pass a compact task brief, not the full conversation, and should stop delegation when another agent would add less value than cost.

### Existing-project onboarding

When the Operator asks to adopt, onboard, or integrate Sterling Flow into an existing project, read [references/onboarding.md](references/onboarding.md). Treat the audit as S2 workflow work: inspect first, delegate the read-only audit to Architect, use Sentinel to review conflicts and risks, obtain explicit Operator approval, then delegate approved changes to Engineer. Do not modify project workflow files during the audit.

Sterling Flow is runtime-agnostic. If the harness provides native multi-agent coordination, use parallel agents only for genuinely independent workstreams and keep the same task brief and approval gates. Do not require a beta platform feature for the basic workflow; sequential role handoffs remain the portable default. OpenAI’s current guidance also recommends choosing reasoning effort intentionally and benchmarking quality, cost, and latency rather than assuming the highest setting is best.

## 4. Risk Levels & Routing Paths

Evaluate task risk before spawning multi-agent chains:

- **`S0` (Trivial / Docs / Formatting):** Direct execution by the current agent, or by **Helper** for pure text transformation. Do not spawn a chain.
- **`S1` (Low Risk / Localized Bugfix):** **Engineer** (`Terra`, medium effort) implements directly with tests. Sentinel pre/post review is skipped.
- **`S2` (Medium Risk / Multi-file / API changes):** Full Chain: `Architect` → `Sentinel` (Pre-gate) → **explicit Operator Approval** → `Engineer` (`Sol`, high effort) → **Operator Review**.
- **`S3` (High Risk / Auth / Data Schema / Invariants):** Mandatory Full Chain: `Architect` → `Sentinel` (Pre-gate) → **explicit Operator Approval** → `Engineer` (`Sol`) → `Sentinel` (Post-gate) → **explicit Operator Approval** → `Ops`.

---

## 5. Subagent Roster

Detailed prompts, constraints, and contracts are in [references/agents.md](references/agents.md):

1. **Architect (`Terra` / Medium / Fast):** Scoping, user flows, constraints, acceptance criteria. *Read-only.*
2. **Sentinel (`Terra` / High / Normal):** Independent risk gatekeeper. Issues verdicts (`GO`, `CONDITIONAL GO`, `INSUFFICIENT EVIDENCE`, `NO-GO`). *Read-only.*
3. **Engineer (`Sol` or `Terra` / Medium or High / Normal):** Directly modifies code and authors tests. Outputs high-level bulleted changes, design liberties/rationales, and test metrics (count created + execution outcome).
4. **Ops (`Terra` / Medium / Fast):** Reads project docs (`README.md`, `DEPLOYMENT.md`, configs) to handle commits, pushes, and deployments **only when explicitly ordered by the Operator**.
5. **Helper (`Luna` / Low / Fast):** Leaf text worker for simple transforms, summaries, and formatting.

---

## 6. Orchestration Flow & Gating Rules

```mermaid
flowchart TD
    Op(["Operator Request"]) --> Mode{"Mode"}
    
    Mode -->|"Direct Chat"| DirectAgent["Individual Agent: Architect / Sentinel / Engineer / Ops"]
    DirectAgent --> Op
    
    Mode -->|"Structured Pipeline"| Triage{"Risk Triage"}
    
    Triage -->|"S0 / S1 Fast-Path"| Eng["Engineer / Ops"]
    Eng --> OpReview(["Operator Review & Release Command"])
    
    Triage -->|"S2 / S3 Full Chain"| Arch["Architect"]
    Arch -->|"Spec & Risk Context"| SentPre["Sentinel Pre-Gate"]
    SentPre -->|"Verdict"| OpGate{"Operator Approval"}
    OpGate -->|"Approved"| EngSol["Engineer (Sol)"]
    OpGate -->|"Revise / Reject"| Op
    
    EngSol -->|"S3: Summary + Tests"| SentPost["Sentinel Post-Gate"]
    EngSol -->|"Standard S2"| OpReview
    SentPost -->|"Post Verdict"| OpReview
    
    OpReview -->|"Explicit Release Command"| Ops["Ops Agent"]
    Ops --> OpFinal(["Operator Final Summary"])
```

For full Sentinel review rules and escalation triggers, consult [references/sentinel-protocol.md](references/sentinel-protocol.md).
