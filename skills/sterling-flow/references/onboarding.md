# Existing-Project Onboarding

Use this workflow when the Operator asks to adopt, onboard, or integrate Sterling Flow into an existing project.

## Objective

Introduce the minimum Sterling Flow guidance needed for the project while preserving useful project-specific conventions. Do not replace working processes merely to make them look standardized.

## Phase 1: Read-only audit

Treat onboarding as S2 workflow work. The coordinating agent should delegate the audit to **Architect (Terra)** with this scope:

- Inspect `AGENTS.md`, project instructions, agent definitions, CI/release documentation, and relevant configuration.
- Identify current planning, implementation, review, testing, commit, and deployment practices.
- Map existing practices to Sterling Flow roles and S0–S3 routing.
- Identify conflicts, duplicated rules, missing approval boundaries, and project-specific requirements.
- Do not modify, delete, rename, or deprecate files or workflow steps.

Architect returns:

1. Current workflow summary.
2. Role and process mapping.
3. Conflicts and gaps.
4. Minimal adoption plan.
5. Acceptance criteria for the integration.

## Phase 2: Independent review

Send the audit and proposed adoption plan to **Sentinel (Terra)**. Sentinel reviews risk, scope, compatibility, and whether the proposed changes are actually necessary. Sentinel returns a verdict and required conditions.

The Operator decides whether to proceed. Engineer must not modify the project before explicit approval.

## Phase 3: Approved integration

After approval, delegate implementation to **Engineer (Sol)** when multiple workflow/configuration files or role contracts change. Engineer must:

- Apply only the approved changes.
- Preserve project-specific rules unless explicitly deprecated.
- Add concise local guidance rather than copying the entire skill.
- Update references and remove contradictions where approved.
- Validate links, configuration syntax, and any documented commands.

For a documentation-only, single-file adjustment, Engineer (Terra) may implement it as S1 after the audit.

## Completion report

Return:

- Files changed and why.
- Existing rules preserved or intentionally superseded.
- Validation performed and results.
- Remaining adoption work or residual risk.

Do not commit, push, deploy, or alter external systems unless the Operator explicitly orders it.
