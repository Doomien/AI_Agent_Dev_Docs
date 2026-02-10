# Agent Documentation Framework v02

**Owner:** Workflow lead  
**Status:** Active  
**Last Updated:** February 9, 2026  
**Source of Truth:** `v02/README.md` for v02 structure and usage

**Streamlined multi-agent collaboration for shared codebases**

---

## Overview

This is a minimal documentation framework designed for **multiple AI agents** working on the same codebase. The focus is on **information exchange** — ensuring agents can pick up where others left off without losing context.

## Files

| File | Purpose | Size Target |
|------|---------|-------------|
| [WORKFLOW.md](./WORKFLOW.md) | How agents should work | Static reference |
| [CONTEXT.md](./CONTEXT.md) | Current state + recent handoffs | < 100 lines |
| [PROGRESS.md](./PROGRESS.md) | What's planned + what's done | Living document |
| [HANDOFF_TEMPLATE.md](./HANDOFF_TEMPLATE.md) | Planning-to-execution handoff contract | Per handoff |
| [MIGRATION_NOTES.md](./MIGRATION_NOTES.md) | v01 -> v02 migration guidance | Read once per project |

## Key Principles

1. **Agents lose context constantly** — Design for amnesia
2. **Less is more** — Verbose docs don't get read
3. **Recency matters** — Archive old entries, keep active context lean
4. **Conflict prevention** — Claim tasks before starting

## Quick Start

1. Copy this `v02/` folder into your project's `docs/` directory
2. Fill in project-specific details in CONTEXT.md and PROGRESS.md
3. Have every agent read WORKFLOW.md once, then read both CONTEXT.md and PROGRESS.md at each session start

## Required External References

v02 is intentionally minimal. Keep these as mandatory companion docs:

- Engineering quality standards: `docs/General Engineering Standards.md`
- Canonical git workflow: `Sandbox/contributing.md`

## Comparison to v01

| Aspect | v01 | v02 |
|--------|-----|-----|
| Document count | 6 files | 5 files |
| Workflow guide | 550+ lines | ~100 lines |
| Focus | Solo dev with AI | Multi-agent collaboration |
| Handoff model | Separate Handoff.md | Integrated into CONTEXT.md |
| Progress tracking | Separate Devlog + ProjectPlan | Combined PROGRESS.md |

---

**Version:** 2.0  
**Optimized for:** Multi-agent context exchange
