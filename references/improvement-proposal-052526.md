# Technical Proposal: Framework v3.0 Core Content Enhancements

**Date:** May 25, 2026  
**Document ID:** `Improvement-Proposal-052526`  
**Status:** Partially Integrated (2026-05-25)
**Author:** Antigravity (AI Coding Assistant)  

---

## Integration Status

This proposal has been reviewed and **partially adopted** into the framework. Note the following before referencing:

| Pillar | Status | Notes |
|--------|--------|-------|
| 1. Vertical Atomic Slicing | **Adopted** (softened) | Default to one commit per task; combine inseparable tasks when needed. "Compiles independently" reframed as "leaves tree in valid state." |
| 2. High-Density Commit Format | **Partially adopted** | Rationale-in-body kept. Canonical format is **Conventional Commits** (lowercase), not `[Bracket]` form. The `Files Touched:` field was **rejected** — `git show --stat` already tracks it. |
| 3. Pre-Commit Testing Mandates | **Adopted** with carve-out | "Test and implementation must land before `[x]`" — order is flexible. Doc-only projects substitute markdown lint / link check. |
| 4. Visual Escalation Thresholds | **Adopted** | Slots into HANDOFF.md "Next Session & Blockers" section under `[B]` status. |

See [`guides/contributing.md`](../guides/contributing.md) for the canonical commit format and [`templates/WORKFLOW.md`](../templates/WORKFLOW.md) for slicing and escalation rules as actually implemented.

---

## 1. Executive Summary

As agentic development evolves from chat-based assistants to autonomous multi-agent pipelines, the core protocols governing code commits, validation, and human escalations must become more precise. This document proposes **content and protocol enhancements** for the Framework v3.0 specification. 

The focus is on four major pillars:
1. **Incremental Atomic Slicing:** Transitioning from monolithic task updates to vertical slicing with atomic git commits.
2. **High-Density Commit Expectations:** Mandating structured, evidence-backed commit messages.
3. **Pre-Commit Testing Mandates:** Enforcing local test-suite validation before code is staged.
4. **Visual & Browser Escalation Thresholds:** Explicit rules for when to handover visual/UX checks to human testing.

---

## 2. Pillar 1: Incremental Atomic Slicing (Git Workflow)

### The Problem
Agents often implement entire complex systems (e.g., an inventory feature plus the backend API plus the database migrations) in a single massive session. This results in:
* Large, unreviewable pull requests.
* Severe context bloat during troubleshooting (if a bug is introduced, pinpointing it is extremely difficult).
* High risk of conflicts when multiple agents share the codebase.

### The v3.0 Protocol: Vertical Slicing
We propose mandating **Vertical Atomic Slicing**. A feature must be broken down into the smallest testable increments that compile independently.

```
Monolithic (v2)  [===================== Implement Inventory UI & API =====================] -> 1 Commit
                                                   vs.
Vertical (v3)    [Slice 1: JSON schema] -> [Slice 2: API Get] -> [Slice 3: UI Grid] -> [Slice 4: UI Drag]
                 (4 Atomic, verified commits)
```

#### Operational Rules:
1. Every task code in `PROGRESS.md` (e.g., `T-001`) must represent a single, vertical slice.
2. An agent **must never** group multiple task completions into a single git commit.
3. As soon as a single task satisfies its acceptance criteria and passes validation, it must be committed immediately. This keeps the git history granular and allows instant rollback if needed.

---

## 3. Pillar 2: High-Density Commit Message Standards

### The Problem
AI-authored commit messages frequently contain generic descriptions (e.g., `docs: update files` or `fix: fix bugs`) or explain *what* changed instead of *why* and *how it was validated*.

### The v3.0 Protocol: The Evidence-Backed Commit Format
Every commit message authored by an agent (or human adopting the IC/QA perspectives) must follow a structured, three-part format that acts as an immutable contract of work.

#### The Format:
```text
[Type] Concise description of the slice (Max 50 chars)

- Rationale: Detailed 'why' behind this change. Explain technical decisions.
- Files Touched: List absolute paths of modified or new files.
- Verification: The exact command executed and proof of successful validation.
```

#### Good Example:
```text
[Feature] Implement player inventory JSON data schema

- Rationale: Established the baseline structure for items, defining ids, 
  sprite mappings, and default properties to support Phaser loading.
- Files Touched:
  * public/data/entities/player-inventory.json
  * templates/general-engineering-standards.md
- Verification: Schema validated successfully against Draft-07 specs using ajv-cli.
```

---

## 4. Pillar 3: Pre-Commit Unit Testing Mandates

### The Problem
A common agent failure pattern is the "build and pray" approach—staging and committing code before confirming that the changes didn't break existing features or that the new logic functions correctly under edge cases.

### The v3.0 Protocol: Test-Driven Handoffs
We propose a strict rule: **No validation proof, no staging.**

```
                     ┌──────────────────────────────┐
                     │     Code Modified by IC      │
                     └──────────────┬───────────────┘
                                    │
                                    ▼
                     ┌──────────────────────────────┐
                     │    Write / Update Unit Test  │
                     └──────────────┬───────────────┘
                                    │
                                    ▼
                     ┌──────────────────────────────┐
                     │    Run Test Suite Locally    │
                     └──────────────┬───────────────┘
                                    │
            ┌───────────────────────┴───────────────────────┐
            ▼ (Pass)                                        ▼ (Fail)
 ┌─────────────────────────────┐                 ┌─────────────────────────────┐
 │  Commit & Update HANDOFF.md  │                 │  Debug & Prevent Staging    │
 └─────────────────────────────┘                 └─────────────────────────────┘
```

#### Operational Rules:
1. **The Test-per-Change Constraint:** Any commit that alters application logic (e.g. math algorithms, data parsers, state machines) *must* include a corresponding unit or integration test inside the same commit.
2. **Pre-Staging Verification:** Before executing `git add`, the outgoing agent must execute the project's local test suite (`npm run test`, `pytest`, etc.).
3. **Evidence Log:** The terminal command output or testing receipt must be documented in `HANDOFF.md` under the QA Verification section.

---

## 5. Pillar 4: Visual & Browser Escalation Thresholds

### The Problem
Large Language Models excel at syntactic checks, logic, and structural audits, but they are "visually blind" to spatial layout, alignment, color harmony, and overall user experience (UX) aesthetics. Headless browser tests can check if a DOM element exists, but cannot detect overlapping text, misaligned grid margins, or awkward animation timings.

### The v3.0 Protocol: Visual Handoff Thresholds
We propose a strict boundary defining when the QA perspective must halt automated execution and escalate the task to **human-driven browser testing**.

#### Escalation Threshold Matrix:

| Category | Trigger Condition (Halt & Escalate) | Action Required |
|----------|------------------------------------|-----------------|
| **Layout & Grid** | Any modifications to CSS Flexbox, Grid layouts, padding/margins, or relative sizing (`rem`, `vh`, `vw`). | QA marks task `[B]` (Blocked/Awaiting Audit) and generates a preview URL for human visual sign-off. |
| **Color & Theme** | Alterations to color schemes, light/dark mode transitions, CSS custom properties, or contrast ratios. | Human verifies accessibility and color harmony on multiple devices. |
| **Interactive UX** | Complex interactive states, drag-and-drop zones, swipe gestures, or canvas rendering (e.g., Phaser viewports). | Human manually playtests the flow and checks responsiveness. |
| **First Load** | First-time page initialization, loading spinners, font rendering, or layout shifts (CLS). | Human audits page loading sequence. |

#### Escalation Handoff Format:
When a threshold is triggered, the agent updates `HANDOFF.md` under Section 6:
```markdown
## 6. Stop Conditions & Human Escalations
- [B] **T-002 Player Grid Layout Shift**
  - **Reason for Escalation:** CSS grid updated to fit mobile viewports. Needs visual layout audit.
  - **Review Link:** Localhost:3000 or staging link: https://preview-dev.net/recipe-app
  - **Auditor:** Human Developer
```

---

## 6. Next Steps for Framework Integration

To integrate these content enhancements into the active v3.0 template set:
1. **Update [templates/WORKFLOW.md](../templates/WORKFLOW.md):** Incorporate the Vertical Slicing operational rules and the Human-Driven Browser Escalation thresholds.
2. **Update [templates/HANDOFF.md](../templates/HANDOFF.md):** Align the layout of Section 3 and Section 4 to strictly enforce the "staged test evidence" and "validation command logs."
3. **Update [guides/contributing.md](../guides/contributing.md):** Incorporate the canonical Conventional Commits format with rationale-in-body. (Note: the original three-part "Evidence-Backed" format was modified during integration — see Integration Status header.)
