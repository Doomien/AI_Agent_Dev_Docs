# Active Context

**Owner:** Active IC Agent/Perspective
**Status:** !!FILL-IN!! (replace with: Active Log / Stale / Archived)
**Last Verified:** !!YYYY-MM-DD!!
**Source of Truth:** `docs/CONTEXT.md` for active session state

<!--
STALE CHECK:
- If "Status" or "Last Verified" still shows !!...!! markers, this template was not initialized — treat as empty.
- If Last Verified is more than 7 days old, the sections below may be wrong. Re-confirm before relying on them.
COMPACTION BUDGET: target < 100 lines total. See "Compaction triggers" in WORKFLOW.md.
-->

**Project:** !!PROJECT NAME!!
**Current System State:** 🟢 Working | 🟡 In Progress | 🔴 Broken — pick one, delete the others

---

## 1. Active Focus

> What any incoming agent needs to read FIRST to orient itself in the codebase.

* **Current Focus:** <!-- e.g., Implementing Phaser player inventory UI -->
* **Active Branch:** <!-- e.g., feature/player-inventory -->
* **Known Blockers:** <!-- e.g., Need assets for potion icon -->

### Key Files & Modules

| File / Path | Role in Current Focus |
|-------------|-----------------------|
| | |

---

## 2. Sliding Handoff Log

<!--
COMPACTION TRIGGER: when a 4th entry is added, do this in the same edit:
  1. Extract permanent decisions from the oldest entry into Section 3.
  2. Delete the oldest entry.
This is a trigger, not a ritual — only run when the threshold is crossed.
-->

### 🟢 Session 1 (Latest) — !!YYYY-MM-DD!! — [Agent Type/Perspective]
* **Modified:** <!-- key files only; full diff is in git -->
* **Objective Fulfilling:** <!-- e.g., T-002 Add item pick-up logic -->
* **Outcome:** <!-- Working / Needs Audit / Blocked -->
* **Handoff Payload:** <!-- 1-2 sentences on what the next session should pick up -->
* **Gotchas:** <!-- Surprises only. Skip if none. -->

---

### 🟢 Session 2 — !!YYYY-MM-DD!! — [Agent Type/Perspective]
* **Modified:**
* **Objective Fulfilling:**
* **Outcome:**
* **Handoff Payload:**
* **Gotchas:**

---

### 🟢 Session 3 (Oldest) — !!YYYY-MM-DD!! — [Agent Type/Perspective]
* **Modified:**
* **Objective Fulfilling:**
* **Outcome:**
* **Handoff Payload:**
* **Gotchas:**

---

## 3. Permanent Architecture Decisions Log

> Long-term technical decisions that persist across sessions. One row per decision — keep dense.

| Topic / System | Decision Taken | Rationale | Date |
|----------------|----------------|-----------|------|
| | | | |

---

## 4. Active Dependencies

| Service / API / DB | Role in Project | Endpoint / Connection Info |
|--------------------|-----------------|----------------------------|
| | | |
