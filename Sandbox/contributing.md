# Repository Contribution Protocols

**Canonical source of truth for git workflow in this repository.**

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
4. Commit:

```bash
git add .
git commit -m "docs: concise description of change"
```

5. Push branch and open pull request:

```bash
git push -u origin docs/[agent]-[task-slug]
```

6. Merge after review/approval. Do not merge directly from local shell unless explicitly authorized.

## Pull Request Checklist

- Scope is limited to the intended task.
- References and links are valid.
- No contradictory guidance introduced.
- Documented behavior matches repository policy.

## Notes for AI Agents

- If instructions conflict across docs, follow this file.
- If a doc appears stale, update it or link it back to this canonical protocol.

