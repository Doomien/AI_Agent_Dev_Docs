# Agent Workflow Guide

**Owner:** Workflow Architect
**Status:** Active Reference
**Last Verified:** 2026-05-25
**Source of Truth:** `templates/WORKFLOW.md` for session operations, cadence, and compaction

<!-- STALE CHECK: if Last Verified is more than 6 months old, sections below are advisory only. -->

This is the canonical session protocol. Skim Sections 1–3 once, then revisit Section 4 (cadence) and Section 5 (compaction) when you're unsure whether to update a doc.

---

## 1. Quick Reference Cycle

```
┌─────────────────────────────────────────────────────────────────┐
│  1. READ CONTEXT.md + PROGRESS.md (cold start bootstrap)        │
│  2. ADOPT ACTIVE PERSPECTIVE (PM, IC, or QA hat) — or skip      │
│     to Lightweight Track for small tasks (see Section 3)        │
│  3. EXECUTE SCOPED WORK; commit per slice                       │
│  4. UPDATE DOCS BY CADENCE (Section 4 — not every slice!)       │
│  5. CHECK COMPACTION TRIGGERS (Section 5 — only if crossed)     │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. The Three Perspectives (PM → IC → QA)

Cycle through these "hats" — whether you're three separate agents or one LLM swapping personas.

### 2.1 PM (Strategic Planner)
* **Goal:** Clarify scope, sequence tasks, prevent design drift.
* **Tasks:** Translate goals into `PROGRESS.md` entries with **explicit, verifiable acceptance criteria**. Resolve blockers. Record architectural decisions in `CONTEXT.md` Section 3.
* **Must not:** Implement production code during a planning session.

### 2.2 IC (Tactical Builder)
* **Goal:** Implement exactly what's in the active task's acceptance criteria.
* **Tasks:** Claim a task with `[/]` in `PROGRESS.md`. Build the smallest vertical slice that leaves the tree in a valid state (tests pass, lint passes, build green). Commit per slice using Conventional Commits.
* **Must not:** Redesign architecture or alter schemas without escalating.

### 2.3 QA (Quality Verifier)
* **Goal:** Prove the IC's changes work and don't regress.
* **Tasks:** Run test suite / typechecker / linter. Verify each acceptance criterion explicitly. Log results in `HANDOFF.md` Section 4.
* **Must not:** Mark `[x]` based on assumptions — only on actual validation.

---

## 3. Lightweight Track (When Three Hats is Overkill)

The PM/IC/QA loop is **optional by task size**. For trivial work, the ceremony costs more than it saves. Use the lightweight track when:

| Signal | Use lightweight? |
|--------|------------------|
| Typo fix, single-file config tweak, dependency bump | Yes |
| Doc edit that touches < 50 lines and no behavior | Yes |
| One-line bug fix with obvious root cause | Yes |
| Refactor touching one module, no API change | Maybe — skip PM, keep IC+QA |
| Anything touching schemas, auth, payment, or data migrations | No — full loop |
| Net-new feature, multi-file changes, or anything ambiguous | No — full loop |

**Lightweight track procedure:**
1. Single perspective (typically IC). No HANDOFF.md update needed if you're not actually handing off.
2. One commit (or two if separating test from impl).
3. Update `PROGRESS.md` only if the task was already tracked there.
4. CONTEXT.md untouched unless the change is architecturally significant.

If you find yourself adding a new acceptance criterion mid-task, scope-creeping into another file, or unsure whether the change is safe — promote to the full loop.

---

## 4. Update Cadence

The single most important rule: **doc updates have a cost.** Stale-by-omission is fine; stale-by-misleading is not.

| Doc | Update when… | Do NOT update when… |
|-----|--------------|---------------------|
| **Commit messages** | Every commit | n/a |
| **PROGRESS.md** | Claiming `[/]`, completing `[x]`, adding/discovering tasks, hitting `[B]` | Mid-slice. Don't rewrite acceptance criteria — that's scope drift. |
| **HANDOFF.md** | Handing off to a different agent, different perspective, or ending a session | After every slice in a continuous same-hat session |
| **CONTEXT.md** | Phase boundary, architectural decision lands, blocker changes, or after compaction trigger fires | Per slice. Per commit. Per session if nothing changed. |
| **AGENTS.md / standards / guides** | Policy or structure changes | During feature work — separate PR |

**Rule of thumb:** if updating the doc takes longer than the slice that justified it, the doc is too heavy *or* the slice is too small.

### Transactional vs. durable docs

* **Transactional** (`PROGRESS.md`, `HANDOFF.md`) — low ceremony, frequent touches. Stale entries get rotated out.
* **Durable** (`CONTEXT.md` decision log, `general-engineering-standards.md`, guides) — high ceremony, rare touches. Every edit should be intentional.

Treating them with the same level of formality is the most common over-update failure mode.

---

## 5. Compaction — Trigger-Based, Not Ritual

Old protocol: "run compaction at the end of every session." Reality: agents forget. New protocol: **visible thresholds, checked when crossed.**

### 5.1 CONTEXT.md — Sliding handoff window
* **Trigger:** adding a 4th entry to Section 2.
* **Action in the same edit:**
  1. Extract any permanent decisions from the oldest entry into Section 3 (Architecture Decisions Log) as a dense one-row summary.
  2. Delete the oldest entry.
* **Budget:** total file < 100 lines.

### 5.2 PROGRESS.md — Archive sweep
* **Trigger:** Active Sprint completes, or Completed section exceeds 15 tasks.
* **Action:**
  1. Move completed `[x]` tasks to Section 5 as a single phase row.
  2. Keep only `[ ]`, `[/]`, and `[B]` in the active sprint view.
* **Budget:** total file < 150 lines; Active Sprint section ≤ 10 tasks.

### 5.3 HANDOFF.md — Overwrite, don't accumulate
* **Trigger:** new handoff.
* **Action:** overwrite the file. HANDOFF is not a log — it's a single live transition note. Prior handoff content survives in git history if needed.

If you find yourself thinking "I should compact this" outside a trigger, you probably shouldn't. Triggers exist so you don't have to judge.

---

## 6. Escalation Rules

Stop work immediately and consult the human when:
- Implementation would change critical data structures or database schemas.
- Active task goals conflict with engineering standards.
- Acceptance criteria are technically impossible as written.
- You hit security, financial, or permission boundaries.
- A visual/UX change needs human eyes (CSS layout, color/theme, interactive UX, page-load behavior) — mark the task `[B]` in `PROGRESS.md` and document the preview URL in `HANDOFF.md` Section 5.
