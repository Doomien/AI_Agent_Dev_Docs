# 2-Week Execution Backlog

**Owner:** Workflow lead  
**Status:** Historical baseline plan  
**Last Updated:** February 9, 2026  
**Source of Truth:** `v01/BACKLOG_2_WEEKS.md` for initial v1 execution plan

**Start date:** February 9, 2026  
**Duration:** 10 working days  
**Goal:** Ship v1-ready docs system for predictable AI-agent handoff workflows

## Milestones

1. M1 (End of Week 1): Core structure is canonical and contradiction-free
2. M2 (End of Week 2): Worked example and metrics instrumentation are complete

## Week 1: Foundation and Canonical Flow

### Task 1: Canonical Root Navigation
- Owner: Docs lead
- Deliverable: Root `README.md` defining user entry path and scope
- Acceptance criteria:
- `README.md` links product definition, backlog, and templates
- First-time user path is <= 5 steps

### Task 2: Author Missing Agent Role Spec
- Owner: Workflow lead
- Deliverable: `.docs/templates/AgentRoles.md`
- Acceptance criteria:
- Defines PM and IC roles, boundaries, escalation rules, and handoff expectations
- All references in `.docs/templates/` resolve to an existing file

### Task 3: Resolve Git Workflow Contradictions
- Owner: Repo maintainer
- Deliverable: One canonical git workflow document with links from secondary docs
- Acceptance criteria:
- `Sandbox/contributing.md` and `Sandbox/git_procedures.md` no longer conflict
- Branch, commit, PR, and merge steps are explicit and consistent

### Task 4: Canonical vs Reference Layering
- Owner: Information architect
- Deliverable: Classification of docs into `core path` and `reference`
- Acceptance criteria:
- Root `README.md` marks core and reference sections
- Long-form philosophy docs are linked as optional context, not required startup reading

## Week 2: Usability and Validation

### Task 5: Worked Example Docs Set
- Owner: Workflow lead
- Deliverable: One filled example set (`ProjectPlan`, `Devlog`, `ContextChain`, `Handoff`)
- Acceptance criteria:
- Example demonstrates one full PM -> IC -> PM cycle
- Example can be followed without external explanation

### Task 6: Session Quality Checklist
- Owner: Quality lead
- Deliverable: Lightweight checklist for "valid handoff" and "session complete"
- Acceptance criteria:
- Checklist is <= 1 page
- Checklist mapped to success metrics in `PRODUCT_V1.md`

### Task 7: Metrics Capture Method
- Owner: Ops/docs maintainer
- Deliverable: Procedure for recording v1 metrics from session artifacts
- Acceptance criteria:
- Defines how to measure time-to-handoff, handoff completion rate, and role compliance
- Includes sampling cadence and ownership

### Task 8: v1 Readiness Review
- Owner: Maintainer + one external reviewer
- Deliverable: Review note confirming release criteria pass/fail
- Acceptance criteria:
- Each criterion in `PRODUCT_V1.md` section 10 has explicit pass/fail status
- Open gaps converted into post-v1 backlog items

## Dependencies

1. Task 2 depends on current template references audit
2. Task 5 depends on Task 2 role definitions
3. Task 8 depends on Tasks 1-7 completion

## Suggested Daily Cadence

1. 15 minutes: prioritize and assign backlog tasks
2. 60-120 minutes: execute one high-impact task
3. 15 minutes: record status and blockers

## Definition of Done (2-week window)

- All Week 1 and Week 2 tasks complete
- All v1 release criteria in `PRODUCT_V1.md` are satisfied or explicitly deferred
- No unresolved contradictions in core workflow docs
