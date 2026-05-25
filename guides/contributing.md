# Repository Contribution Protocols

**Canonical source of truth for git workflow in this repository.**

**Last Verified:** 2026-05-25

<!-- STALE CHECK: if Last Verified is more than 6 months old, re-confirm branch protection rules and PR review settings before relying on this doc. -->

## Policy

- `main` is protected and read-only for direct agent commits.
- All work happens on a feature branch.
- Merge to `main` happens through pull request review.

## Branch Naming

Use: `type/agent-task-description`

- `feature/` for new capabilities
- `fix/` for bug fixes
- `docs/` for documentation updates
- `refactor/` for internal cleanup

Examples:
- `docs/gpt-add-agentroles-template`
- `fix/local-correct-git-workflow-link`

## Standard Workflow

1. Sync `main`:

```bash
git checkout main
git pull origin main
```

2. Create a branch:

```bash
git checkout -b docs/[agent]-[task-slug]
```

3. Make scoped changes and validate them.
4. Commit (see format below):

```bash
git add <specific-files>
git commit -m "docs: concise description of change"
```

5. Push branch and open pull request:

```bash
git push -u origin docs/[agent]-[task-slug]
```

6. Merge after review/approval. Do not merge directly from local shell unless explicitly authorized.

## Commit Message Format — Canonical

Use **Conventional Commits** (lowercase). This is the canonical format for this repo and matches actual git history.

```
type(scope): short description

Optional body: explain WHY, not WHAT. Wrap at 72 chars.
Reference issues with: Closes #123
```

**Types:** `feat`, `fix`, `docs`, `refactor`, `perf`, `test`, `chore`, `style`, `build`, `ci`.

**Scope** is optional but useful when multiple areas change.

**Examples:**

```
docs(workflow): add cadence table

Captures the transactional-vs-durable distinction so agents
don't over-update CONTEXT.md and PROGRESS.md.
```

```
feat(inventory): add player pickup collision

Closes #42
```

```
fix(physics): prevent tunneling at high velocity
```

**Do not include:**
- A `Files Touched:` list. `git show --stat` already tracks it; duplicating just rots.
- Generated `Co-Authored-By:` lines unless the user explicitly asks for them.

## Pull Request Checklist

- Scope is limited to the intended task.
- References and links are valid.
- No contradictory guidance introduced.
- Documented behavior matches repository policy.
- Commit messages follow Conventional Commits.

## Notes for AI Agents

- If instructions conflict across docs, follow this file (for git policy) and [`templates/WORKFLOW.md`](../templates/WORKFLOW.md) (for session protocol).
- If a doc appears stale, update it or link it back to this canonical protocol.
- Stage specific files (`git add path/to/file`) rather than `git add -A` or `git add .` — that habit prevents accidentally committing secrets, large binaries, or out-of-scope changes.
