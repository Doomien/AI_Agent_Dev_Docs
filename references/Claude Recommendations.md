# Claude Recommendations

High-impact suggestions for making this documentation collection more useful for guiding agent collaboration.

---

## 1. Bridge the Two Disconnected Frameworks

The repo currently has **two separate worlds** that don't talk to each other:
- **Sandbox/** — operational guides (git workflows, deployment, philosophy)
- **.docs/templates/** — a structured PM/IC agent framework with templates

There's no document that connects them. A developer (or agent) landing in `.docs/templates/` would never discover the microservice workflow guide, and vice versa. **Creating a root `README.md`** that maps the reader's journey through both halves — and adding cross-references between them — would turn these from isolated documents into an integrated system. Right now the whole is less than the sum of its parts.

---

## 2. Create the Missing `AgentRoles.md`

The templates framework references `AgentRoles.md` repeatedly (`QUICK_REFERENCE.md`, `.docs/templates/README.md`) — it's the foundational document that defines the PM_Agent and IC_Agent roles, their responsibilities, and handoff protocols. **It doesn't exist** and never has (confirmed via git history — it was a forward reference to a document that was planned but never authored).

This is the single biggest gap: without it, the entire two-tier agent system described in the framework is under-specified. An agent picking up these docs can't answer the basic question "what am I supposed to do?" Creating this file would immediately make the whole template package functional rather than aspirational.

---

## 3. Resolve the Git Workflow Contradiction

`Sandbox/contributing.md` says `main` is **read-only** — never commit directly. `Sandbox/git_procedures.md` says to **merge directly to main** as the final step. `CLAUDE.md` sides with `contributing.md`. This is the kind of contradiction that will cause an agent to either pick the wrong workflow or stall asking for clarification every time.

Recommendation: **Consolidate these into a single authoritative git workflow document** (or make one clearly canonical and the other a quick-reference that defers to it). A single source of truth for branching, committing, PR creation, and merging would eliminate ambiguity and reduce the redundancy that `CursorRecommendations.md` already flagged.

---

## 4. Add One Completed Example for the Template Set

Every template (`ContextChain.md`, `Devlog_TEMPLATE.md`, `ProjectPlan_Template.md`) shows structure and placeholders but **no filled-in example**. For an agent encountering these for the first time, the difference between "here's a blank form" and "here's what a good one looks like" is enormous — it's the difference between knowing the format and understanding the intent.

A single **worked example** — a small project (even a toy one) with a completed ProjectPlan, a few Devlog entries, and a ContextChain snapshot — would make the entire template system dramatically more usable. Agents learn best from examples, not abstractions.

---

## Priority

| # | Suggestion | Effort | Impact |
|---|-----------|--------|--------|
| 2 | Create `AgentRoles.md` | Low | Fixes broken references across the framework |
| 3 | Resolve git workflow contradiction | Low | Eliminates agent confusion and contradictions |
| 4 | Add a worked example | Medium | Makes the template system learnable |
| 1 | Root `README.md` bridging both halves | Medium | Transforms the repo from a collection into a system |
