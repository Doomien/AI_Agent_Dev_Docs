# Git Procedures (Reference)

This file is a quick reference only.

For the canonical and complete workflow, use:
- [`Sandbox/contributing.md`](contributing.md)

## Quick Command Sequence

```bash
git checkout main
git pull origin main
git checkout -b docs/[agent]-[task-slug]
# make changes
git add .
git commit -m "docs: concise description"
git push -u origin docs/[agent]-[task-slug]
```

Open a pull request and merge after approval.

