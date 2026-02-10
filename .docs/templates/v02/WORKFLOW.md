# Agent Workflow Guide

**Version:** 2.0 | **Purpose:** Multi-agent collaboration on shared codebases

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────┐
│  START SESSION                                          │
│  1. Read CONTEXT.md (what's the current state?)         │
│  2. Check PROGRESS.md (what's planned/in-flight?)       │
│  3. Do your work                                        │
│  4. Update both files before ending                     │
└─────────────────────────────────────────────────────────┘
```

---

## Agent Roles

### PM Agent (Planning)
**Use for:** Architecture decisions, feature design, task breakdown, resolving blockers

**Responsibilities:**
- Create and update project plans in PROGRESS.md
- Define task acceptance criteria before implementation starts
- Make technical decisions and document rationale
- Break work into discrete, handoffable tasks
- Review phase completions

### IC Agent (Implementation)
**Use for:** Writing code, implementing features, testing, bug fixes

**Responsibilities:**
- Execute tasks from PROGRESS.md
- Document what you changed in CONTEXT.md
- Flag blockers for PM Agent
- Test before marking complete

---

## Non-Negotiable References

- Follow engineering quality standards from `General Engineering Standards.md`.
- Follow git policy from `Sandbox/contributing.md`.
- If workflow instructions conflict, `Sandbox/contributing.md` is canonical for git process.

---

## Information Exchange Protocol

When multiple agents work on the same codebase, **context loss is the enemy**. Follow this protocol:

### Before Starting Work
```markdown
1. Read CONTEXT.md completely
2. Read PROGRESS.md completely
3. Identify any in-progress work that might conflict
4. Claim your task in PROGRESS.md (mark as [/] in-progress)
5. Add an In Flight row using the task ID only
```

### During Work
```markdown
- Make atomic, well-documented commits
- Update CONTEXT.md if you discover important context
- Don't modify files another agent is actively working on
```

### After Completing Work
```markdown
1. Verify all task acceptance criteria are satisfied
2. Update PROGRESS.md (mark task [x] complete)
3. Remove/close the matching In Flight row
4. Add a CONTEXT.md entry describing what you changed and why
5. Note any follow-up work needed
6. Note any gotchas or surprises for future agents
```

---

## Conflict Prevention

| Scenario | Action |
|----------|--------|
| Two agents need same file | One waits, or split file into smaller modules |
| Discovered architectural issue | Stop, document in CONTEXT.md, flag for PM Agent |
| Task is blocked | Mark `[B]` in PROGRESS.md, move to next task |
| Found bug during other work | Log in PROGRESS.md under "Discovered Issues", don't fix unless trivial |

**Single source of truth:** task status lives in `PROGRESS.md` task list. `In Flight` is an index of currently claimed task IDs, not a second task list.

---

## Context Handoff Format

When updating CONTEXT.md, use this format:

```markdown
## [Date] - [Agent Name/Type]

**Changed:** List of files/modules modified  
**Why:** Brief rationale  
**State:** Working / Broken / Needs Review  
**Next:** What the next agent should do or know  
**Gotchas:** Any surprises or non-obvious things  
```

---

## When to Escalate to Human

- Creative direction decisions
- Major architectural pivots  
- Priority conflicts between tasks
- External dependencies or permissions
- Anything with financial/security implications

---

## Files in This Framework

| File | Purpose | Update Frequency |
|------|---------|------------------|
| `WORKFLOW.md` | This file — how to work | Rarely |
| `CONTEXT.md` | Rolling state + handoffs | Every session |
| `PROGRESS.md` | Plan + work log | When tasks change |
