# Repository Contribution Protocols

**ATTENTION AI AGENTS AND AUTOMATED TOOLS:**
This repository follows a strict Feature Branch Workflow. You are NOT authorized to commit directly to the `main` branch. 

Follow the protocols below for every session.

## 1. Branching Strategy
* **Production Branch:** `main` (READ ONLY for agents).
* **Your Workspace:** You must create a new branch for every task.

### Branch Naming Convention
Use the following format: `type/agent-task-description`

* **Prefixes:**
    * `feature/` (New capabilities)
    * `fix/` (Bug repairs)
    * `docs/` (Documentation changes)
    * `refactor/` (Code cleanup)
* **Agent ID:** Identify yourself (e.g., `claude`, `gpt`, `local`, `homelab`).
* **Example:** `feature/claude-add-login-button` or `fix/local-typo-correction`.

### Setup Command
Before writing any code, execute:
```bash
git checkout main
git pull origin main
git checkout -b feature/[your-agent-name]-[task-slug]