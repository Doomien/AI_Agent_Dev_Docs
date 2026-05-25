# AI Agent Dev Docs

Documentation system and templates for AI-assisted software development.

**Cold-start for AI agents:** [`AGENTS.md`](AGENTS.md).

## Start Here

1. **Read the contribution rules:** [`guides/contributing.md`](guides/contributing.md) — git workflow and PR policy.
2. **Run a session using the framework:** [`templates/README.md`](templates/README.md) — PM/IC/QA loop, cadence, compaction.
3. **Progressive deployment patterns:** [`guides/microservice-workflow-guide.md`](guides/microservice-workflow-guide.md) — static → homelab → Docker → GitHub.
4. **Code style baseline:** [`templates/general-engineering-standards.md`](templates/general-engineering-standards.md).

---

## Docs Map

### Operational guides (`guides/`)

Day-to-day rules and procedures:
- [Canonical git workflow](guides/contributing.md) — branching, commit format (Conventional Commits), PR checklist.
- [Git quick reference](guides/git_procedures.md) — short CLI cheat sheet.
- [Microservice workflow guide](guides/microservice-workflow-guide.md) — progressive deployment.

### Framework templates (`templates/`)

Canonical framework for agent-assisted projects. Copy into a new project's `docs/`.
- [Framework overview](templates/README.md)
- [Session workflow](templates/WORKFLOW.md) — PM/IC/QA, cadence, compaction, lightweight track.
- [CONTEXT.md template](templates/CONTEXT.md), [PROGRESS.md template](templates/PROGRESS.md), [HANDOFF.md template](templates/HANDOFF.md)
- [General engineering standards](templates/general-engineering-standards.md)
- [Devlog template](templates/Devlog_TEMPLATE.md), [Project plan template](templates/ProjectPlan/)

### Technical suggestions (`technical-suggestions/`)

- [Tech stack recommendations](technical-suggestions/tech-stack-recommendations.md) — Godot vs. Phaser decision matrix.
- [Phaser project starter](technical-suggestions/phaser-project-template.md).

### Research & memos (`references/`)

Background and proposals, not operational:
- [References index](references/README.md)
- [Claude recommendations](references/claude-recommendations.md)
- [Cursor recommendations](references/cursor-recommendations.md)
- [The case for custom AI tooling](references/the-case-for-custom-ai-tooling.md)
- [Improvement proposal 052526](references/improvement-proposal-052526.md) — partially integrated; see header for status.

### Archive (`archive/`)

Deprecated v01/v02 execution state and earlier framework versions. Not for active use — see [archive README](archive/README.md).

---

## What This Repository Provides

- **A docs operating system** for structured PM/IC/QA agent sessions with strict context budgets.
- **Standard templates** for project plans, progress tracking, and handoffs.
- **Engineering standards** that work across web frameworks.

## Who This Is For

- Solo developers using AI agents for day-to-day implementation.
- Teams validating agent-assisted workflows.
