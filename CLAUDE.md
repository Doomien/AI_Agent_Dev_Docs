# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a documentation repository for AI-assisted software development. It contains guides, templates, and protocols for both AI agents and human developers working on software projects. The repository does NOT contain application code - it's purely documentation and templates.

## Git Workflow - CRITICAL

**MANDATORY: Feature Branch Workflow**
- `main` branch is READ ONLY for AI agents
- NEVER commit directly to `main`
- ALWAYS create a feature branch before any changes

**Branch naming convention:**
```bash
type/agent-task-description
```

Prefixes:
- `feature/` - New capabilities
- `fix/` - Bug repairs
- `docs/` - Documentation changes
- `refactor/` - Code cleanup

**Before any work:**
```bash
git checkout main
git pull origin main
git checkout -b feature/claude-[task-slug]
```

**After completion:**
```bash
git add .
git commit -m "Description of changes"
# Push and create PR - do NOT merge directly to main
```

Refer to `Sandbox/contributing.md` and `Sandbox/git_procedures.md` for complete git procedures.

## Repository Structure

```
/
├── Sandbox/               # Active development documents
│   ├── contributing.md    # Contribution protocols (AI + human)
│   ├── git_procedures.md  # Git workflow guide
│   ├── microservice-workflow-guide.md  # 1300+ line comprehensive guide
│   └── The Case for Custom AI Tooling.md  # Philosophy document
├── .docs/                 # Templates and frameworks
│   └── templates/         # Project templates
│       ├── README.md      # Agent-assisted development framework
│       ├── General Engineering Standards.md
│       ├── ContextChain.md
│       ├── Devlog_TEMPLATE.md
│       ├── QUICK_REFERENCE.md
│       └── ProjectPlan/
└── CursorRecommendations.md  # Meta-documentation about improving these docs
```

## Key Documents Overview

**Sandbox/contributing.md** - Branching strategy and contribution protocols for AI agents
**Sandbox/git_procedures.md** - Basic git workflow for feature additions
**Sandbox/microservice-workflow-guide.md** - Progressive enhancement workflow (Static → Homelab → Docker → GitHub)
**Sandbox/The Case for Custom AI Tooling.md** - Philosophy and rationale for custom AI tooling over generic chat interfaces
**.docs/templates/README.md** - Framework for agent-assisted development with PM/IC agent roles
**CursorRecommendations.md** - Recommendations for improving the documentation structure

## Working with This Repository

### When Adding/Editing Documentation

1. **Always use feature branches** - see Git Workflow above
2. **Reference related documents** - these docs should cross-reference each other
3. **Follow the existing structure** - documents in `Sandbox/` are active development
4. **Check CursorRecommendations.md** for improvement suggestions before making changes
5. **Maintain consistency** - use similar formatting across documents

### Document Relationships

- `contributing.md` and `git_procedures.md` both cover git workflows (some redundancy)
- `microservice-workflow-guide.md` is comprehensive and self-contained (1300+ lines)
- `.docs/templates/` contains a separate framework for agent-assisted development
- All documents assume AI agent involvement in development

### Philosophy

This repository embodies several key principles:
1. **Progressive enhancement** - Start simple, add complexity only when needed
2. **AI-first development** - Processes designed for AI agent workflows
3. **Documentation as protocol** - Docs serve as contracts between agents and humans
4. **Iterative refinement** - Documents are living, meant to evolve

## Common Tasks

### Adding a new guide
- Place in `Sandbox/` directory
- Cross-reference with existing documents
- Add entry to main README (create one if it doesn't exist)
- Follow feature branch workflow

### Updating templates
- Templates are in `.docs/templates/`
- Maintain the TEMPLATE suffix for template files
- Update the templates README if adding new templates

### Improving existing documentation
- Check `CursorRecommendations.md` first for known improvement areas
- Maintain existing structure and formatting patterns
- Test all code examples and commands
- Ensure cross-references remain valid

## Important Notes

- This is a **documentation-only** repository - no application code
- The microservice guide assumes Linux/Docker environments for deployment sections
- Git workflow is strictly enforced for agent work
- Documents reference external tools (Docker, nginx, VSCode, etc.) but don't include them
- Some documents contain detailed setup instructions (e.g., homelab nginx configuration)

## File Naming Conventions

Prefer kebab-case for consistency:
- ✅ `microservice-workflow-guide.md`
- ✅ `git_procedures.md`
- ❌ `The Case for Custom AI Tooling.md` (has spaces - legacy)

When creating new files, use kebab-case or snake_case, avoid spaces.
