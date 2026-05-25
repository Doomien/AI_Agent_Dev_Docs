# Session Handoff

**Owner:** Outgoing Agent / Perspective
**Status:** !!FILL-IN!! (replace with: Active Transition / Stale / Archived)
**Last Verified:** !!YYYY-MM-DD!!
**Source of Truth:** `docs/HANDOFF.md` for active session transition

<!--
STALE CHECK:
- If "Status" or "Last Verified" still shows !!...!! markers, this template was not initialized — treat as empty.
- If Last Verified is more than 3 days old, this handoff is likely cold. Re-read CONTEXT.md and confirm task state before continuing.
WHEN TO UPDATE: at handoff between perspectives/agents/sessions. NOT after every slice in a continuous session.
-->

---

## 1. Transition Header

* **Date:** !!YYYY-MM-DD!!
* **From:** [Outgoing Agent/Perspective, e.g., IC_Builder]
* **To:** [Incoming Agent/Perspective, e.g., QA_Verifier]
* **Task IDs In-Flight:** [e.g., T-001, T-002]
* **Active Branch / PR:** [e.g., feature/player-inventory]

---

## 2. Strategic Objective (PM Hat)

* **Objective:** <!-- What this session aimed to accomplish and why -->
* **In Scope:**
  *
* **Out of Scope:**
  *

---

## 3. Changes & Staged Evidence (IC Hat)

* **Summary of Changes:** <!-- Brief prose — full diff is in git, do not duplicate file lists here -->
* **Implementation Decisions:** <!-- Rationale for non-obvious choices -->
  *
* **Staged Evidence:** <!-- Screenshots, output files, terminal logs proving compile/build -->
  *

---

## 4. Verification & Testing Logs (QA Hat)

> QA must run testing commands and verify each Acceptance Criterion before marking `[x]`.

* **Validation Command Executed:**
  ```bash

  ```
* **Acceptance Criteria Audited:**
  - [ ] **T-001 — Criterion 1:** <!-- verification outcome -->
  - [ ] **T-001 — Criterion 2:**
* **Audit Outcome:** 🟢 Passed | 🔴 Failed (re-escalated to IC)

---

## 5. Next Session & Blockers

> Single section. Covers what to do next AND what would force the next agent to halt.

* **Next Immediate Action:** <!-- First thing the next session/agent should do -->
  *
* **Non-Obvious Gotchas:** <!-- e.g., run `npm install`, env var added, schema migration pending -->
  *
* **Stop Conditions (Halt & Escalate to Human):**
  * <!-- e.g., If T-003 acceptance criterion requires DB migration, stop and confirm. -->
* **Unresolved Risks:**
  *
* **Visual Audit Required `[B]`:** <!-- If a task is blocked pending human visual review (CSS layout, color/theme, interactive UX, page load), note it here with a preview URL. -->
  *
* **Archival Actions Taken:** <!-- e.g., Compacted CONTEXT.md handoff window -->
  *
