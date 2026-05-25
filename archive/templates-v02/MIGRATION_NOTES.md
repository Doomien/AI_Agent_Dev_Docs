# v01 to v02 Migration Notes

**Owner:** Docs maintainer  
**Status:** Active guidance  
**Last Updated:** February 9, 2026  
**Source of Truth:** `v02/MIGRATION_NOTES.md` for migration decisions

## Purpose

This note explains how to move from the verbose v01 planning docs to the lightweight operational v02 workflow.

## What Changed

| Area | v01 | v02 |
|------|-----|-----|
| Planning detail | Long-form, strategy-heavy docs | Lean operational docs |
| Session tracking | Separate planning and backlog artifacts | One live `PROGRESS.md` plus `CONTEXT.md` |
| Handoffs | Implicit across docs | Explicit workflow and template |

## Migration Steps

1. Preserve v01 as historical baseline.
2. Initialize `v02/CONTEXT.md` with current project state and key files.
3. Translate active v01 backlog items into task IDs in `v02/PROGRESS.md`.
4. Adopt `v02/HANDOFF_TEMPLATE.md` for PM-to-IC scope transfer.
5. Keep non-operational analysis in `references/`.

## Mapping Guide

- Product direction and success criteria:
  Use `v01/PRODUCT_V1.md` as reference, then summarize active goals in `v02/PROGRESS.md`.
- Execution status:
  Move active/open tasks from `v01/BACKLOG_2_WEEKS.md` into `v02/PROGRESS.md`.
- Session continuity:
  Record completed changes and next actions in `v02/CONTEXT.md`.

## Operating Rule

v01 remains the baseline context archive. v02 is the day-to-day source of truth for current work.
