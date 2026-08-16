# Sterling Flow ⚡

> **Multi-Agent Orchestration & Token-Optimized Dev Flow for OpenAI Codex**  
> *Architected by Sterling Lab*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Codex Compatible](https://img.shields.io/badge/OpenAI%20Codex-Compatible-green.svg)](https://github.com/openai/codex)
[![Architecture: Multi--Agent](https://img.shields.io/badge/Architecture-Tiered%20Multi--Agent-orange.svg)](#architecture)

---

## 📖 Overview

**Sterling Flow** is not a heavy agent framework, complex runtime, or SDK. It is a lightweight, **opinionated steering skill and workflow protocol** designed to guide multi-agent development in OpenAI Codex environments (CLI, IDEs, desktop apps, and agent platforms).

Most AI coding setups suffer from two extremes:
1. **The Overkill Trap:** Delegating simple 2-line typo fixes or documentation edits to expensive, flagship reasoning models (`Sol`), exhausting subscription token limits rapidly.
2. **The Hallucination Trap:** Letting agents write code without independent verification or unilaterally committing unverified changes to production branches.

**Sterling Flow** solves this by establishing strict role boundaries, hard risk gating (`S0`–`S3`), model-tier routing, and absolute Operator authority.

---

## 🎯 Target Audience

- **Developers on Standard / Low-Cost AI Subscriptions:** Keep token consumption predictable and under budget without sacrificing code quality.
- **Solo Engineers & Tech Leads:** Run a disciplined team of specialized virtual subagents (scoping, implementation, security review, release) while retaining full command over git and deployments.
- **Teams Automating with Codex:** Standardize multi-agent subagent definitions, tool sandboxing, and handoff contracts across any Codex-compatible toolchain.

---

## 🏗️ Architecture & Subagent Roster

Sterling Flow delegates work across 5 specialized subagents, strictly mapped to model capability tiers and token economics:

```mermaid
flowchart TD
    Op(["Operator Request"]) --> Mode{"Mode"}
    
    Mode -->|"Direct 1-on-1 Chat"| DirectAgent["Any Agent: Architect / Sentinel / Engineer / Ops"]
    DirectAgent --> Op
    
    Mode -->|"Structured Pipeline"| Triage{"Risk Triage"}
    
    Triage -->|"S0 / S1 Fast-Path"| FastEng["Engineer / Ops"]
    FastEng --> OpReview(["Operator Review"])
    
    Triage -->|"S2 / S3 Full Chain"| Arch["Architect (Terra)"]
    Arch -->|"Spec & Risk Context"| SentPre["Sentinel Pre-Gate (Terra)"]
    SentPre -->|"Verdict: GO"| OpGate{"Operator Approval"}
    OpGate -->|"Approved"| EngSol["Engineer (Sol)"]
    OpGate -->|"Reject / Revise"| Op
    
    EngSol -->|"S3: Summary + Tests"| SentPost["Sentinel Post-Gate (Terra)"]
    EngSol -->|"Standard S2"| OpReview
    SentPost -->|"Post Verdict: GO"| OpReview
    
    OpReview -->|"Explicit Release Command"| OpsAgent["Ops Agent (Terra)"]
    OpsAgent --> OpFinal(["Operator Final Release Report"])
```

### Subagent Roles

| Agent | Tier | Codex Model | Default Effort | Permissions | Responsibilities |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Architect** | `Terra` | `gpt-5.6-terra` | `medium` | Read-only | Feature framing, user flows, constraints, acceptance criteria. *Never writes code.* |
| **Sentinel** | `Terra` | `gpt-5.6-terra` | `high` | Read-only | Independent risk review (`S0`–`S3`) and gatekeeping (`GO`/`NO-GO`). Rigorously skeptical. |
| **Engineer** | `Sol` | `gpt-5.6-sol` | `high` | Full Read/Write/Exec | Directly writes code, authors test suites, runs validations. Master of code quality. |
| **Ops** | `Terra` | `gpt-5.6-terra` | `medium` | CLI / Git / Deploy | Inspects repo docs to commit, push, and deploy **only when explicitly ordered**. |
| **Helper** | `Luna` | `gpt-5.6-luna` | `low` | Text transform | Leaf subagent for bullet extraction, formatting, and trivial text summaries. |

---

## 🚦 Risk Gating & Fast-Paths

Sterling Flow uses a 4-tier risk matrix to prevent token waste:

| Risk Level | Impact Surface | Route | Token Impact |
| :--- | :--- | :--- | :--- |
| **`S0` (Trivial)** | Typos, comments, markdown docs. | Direct to **Ops** or **Engineer** (`Terra`/`Luna`). | **~90% savings** (skips Architect/Sentinel). |
| **`S1` (Low)** | Localized bugfix, single function tweak. | **Engineer** (`Terra`) implements with tests. | **Fast turnaround** (no Sentinel pre/post gate). |
| **`S2` (Medium)** | Multi-file changes, API signatures, dependencies. | `Architect` → `Sentinel` Pre-gate → **Operator** → `Engineer` (`Sol`). | High reasoning applied only to implementation. |
| **`S3` (Critical)** | Auth, crypto, database schemas, core invariants. | Full Chain + Mandatory `Sentinel` Post-change verification. | Zero-compromise security & stability. |

---

## 💡 Practical Sample Scenarios

### Scenario 1: Quick README Update (`S0`)
* **Prompt:** `"Update the setup instructions in README.md"`
* **Flow:** Operator → Ops (1 hop, `gpt-5.6-terra` / `gpt-5.6-luna`).
* **Result:** Ops updates text, formats conventional commit message, and awaits your command to push.

### Scenario 2: Fix Edge-Case in Date Parser (`S1`)
* **Prompt:** `"Fix time zone parsing offset bug in dateUtils.ts"`
* **Flow:** Operator → Engineer (`gpt-5.6-terra`, medium effort).
* **Result:** Engineer fixes logic, authors 2 new regression unit tests, runs test suite, and outputs summary.

### Scenario 3: Database Schema Migration & Auth Token Refresh (`S3`)
* **Prompt:** `"Migrate session tokens from JWT cookies to Redis with sliding expiration"`
* **Flow:** Full Multi-Agent Chain.
  1. **Architect** produces spec, data flows, and risk context.
  2. **Sentinel** classifies as `S3`, reviews attack surface, issues `CONDITIONAL GO` requiring replay attack tests.
  3. **Operator** approves implementation.
  4. **Engineer (`Sol`)** builds migrations, writes Redis adapter, authors 6 unit/integration tests, runs test suite.
  5. **Sentinel** performs post-implementation review on diff + test output → issues final `GO`.
  6. **Operator** orders **Ops** to commit and trigger deployment.

---

## ⚡ What Do You Gain?

1. **Massive Token & Credit Savings:** Low-risk tasks skip multi-hop chains; heavy reasoning (`Sol`) is reserved strictly for complex code execution.
2. **Zero Hallucinated Commits:** Subagents are forbidden from executing git commits, pushes, or deployments autonomously. The Operator is the sole authority.
3. **No Context Bloat:** Raw code diffs are eliminated from chat output (the IDE handles visual diffing). Deliverables are concise executive summaries (≤ 100 words).
4. **Missing Data Escalation:** Subagents never guess or hallucinate missing repository names, credentials, or deployment targets. They immediately halt and ask.

---

## 🚀 Quickstart & Setup

### 1. Skill Installation (Ready to Use)
Simply copy the `skills/sterling-flow/` folder into your project's skill directory (e.g. `.agents/skills/sterling-flow` or your global skills path). **The skill works immediately out-of-the-box with no extra configuration needed.**

```
skills/sterling-flow/
├── SKILL.md                          # Main orchestrator rules & risk matrix
└── references/
    ├── agents.md                     # Complete prompts, contracts & boundaries
    ├── sentinel-protocol.md          # Change Risk and Review protocol
    └── codex-config-example.toml     # Optional CLI configuration reference
```

### 2. Optional: Enforce Hard Tool Sandboxing
If you want the Codex CLI/runtime to enforce hard tool permissions at the process level (e.g. physically disabling file-write tools for Architect and Sentinel), you can optionally add these declarations to your project's `.codex/config.toml`:

```toml
[multi_agent]
enabled = true
default_subagent_model = "gpt-5.6-terra"
default_subagent_reasoning_effort = "medium"

[agents.architect]
description = "Feature-framing and specification agent"
model = "gpt-5.6-terra"
model_reasoning_effort = "medium"
tools = ["read_file", "list_dir", "grep_search"]  # Strictly read-only

[agents.sentinel]
description = "Independent risk-review and gatekeeping agent"
model = "gpt-5.6-terra"
model_reasoning_effort = "high"
tools = ["read_file", "list_dir", "grep_search"]  # Strictly read-only

[agents.engineer]
description = "Implementation, code editing, and test authoring agent"
model = "gpt-5.6-sol"
model_reasoning_effort = "high"

[agents.ops]
description = "Commit, push, deployment, and release agent"
model = "gpt-5.6-terra"
model_reasoning_effort = "medium"
tools = ["run_command", "read_file", "write_to_file"]

[agents.helper]
description = "Leaf subagent for trivial text transforms and formatting"
model = "gpt-5.6-luna"
model_reasoning_effort = "low"
tools = []
```

---

## 🔄 Adopting Sterling Flow in Existing Projects

If you already have existing project workflows, custom agent instructions, or local gatekeeping rules, you can onboard your project using this standard prompt:

```markdown
We are adopting the `sterling-flow` skill as our standard multi-agent orchestration framework.

Please perform an audit to integrate this skill into our existing project workflow:

1. **Audit Existing Workflow & Agent Definitions:**
   - Inspect our local workflow files, agent configurations, versioning/changelog procedures, and existing role definitions.
   - Compare them against the formal `sterling-flow` skill definitions.

2. **Propose Integration & Consolidation Plan:**
   - Identify obsolete, redundant, or conflicting steps/files (e.g. migrating local gate rules to the formal Sentinel protocol).
   - Show how project-specific requirements (versioning, changelogs, deployments) map to the standardized roles (Architect, Sentinel, Engineer, Ops, Helper).

3. **Gating Rule:**
   - **Do NOT delete, replace, or modify any existing workflow files or steps yet.**
   - Present a clear summary of proposed changes and deprecations to the Operator, and wait for explicit approval before modifying the workspace.
```

---

## 📚 References & Inspiration

Sterling Flow builds upon architectural patterns and community research in multi-agent orchestration:

- **[Eric Provencher (@pvncher) — Practical multi-agent orchestration in Codex](https://x.com/pvncher/status/2080707291603407077)** — Practical methodology for tiered multi-agent delegation, token economics, anti-sycophancy system prompts, and multi-agent coordination.
- **[Daniel Vaughan — Codex CLI Multi-Agent Orchestration v2 Complete Guide](https://codex.danielvaughan.com/2026/04/11/codex-cli-multi-agent-orchestration-v2-complete-guide/)** — Patterns for subagent role configuration, tool permission sandboxing, and parent-to-leaf delegation.
- **[Daniel Vaughan — Agentic Coding with OpenAI Codex CLI](https://danielvaughan.com)** — Foundational methodology for goal-oriented workflows and tiered reasoning.

---

## 📄 License

Distributed under the MIT License. See [LICENSE](LICENSE) for details.

© 2026 **Sterling Lab**. Built for high-efficiency AI-assisted engineering.
