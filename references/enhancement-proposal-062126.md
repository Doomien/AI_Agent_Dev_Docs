# Enhancement Proposal: Spec-Kit-Inspired Additions to kst_promptware

> **Filed:** 2026-06-21
> **Author:** HTF_Coder (based on research synthesis of `github/spec-kit`)
> **Status:** Draft for review
> **Scope:** Lightweight / medium-weight — no runtime CLI, no build step, no new dependencies
> **Pact:** These proposals preserve kst_promptware's core charter: runtime-agnostic, buildless, markdown-native.

---

## 0. The Gap

Spec Kit (`github/spec-kit`) and kst_promptware are converging on the same insight — specification-first development with AI agents — but from different angles.

| Axis | Spec Kit | kst_promptware |
|---|---|---|
| **Delivery** | Python CLI (`specify`) that bootstraps project files and installs agent commands | Pure markdown bundle (charter → constitution → work orders → evidence) |
| **Agent integration** | Formal integration system for 30+ agents with base classes per format | Planned adapter shims (F-007, queued) |
| **Lifecycle** | Constitution → Spec → Plan → Tasks → Implement → Converge | Draft → Released → In-Progress → Review → Complete → Reconciled |
| **Spec debt** | `/speckit.clarify` — explicit underspecification detection | Implicit (human gate), no structured step |
| **Validation** | Cross-artifact analysis (`/speckit.analyze`) + checklists (`/speckit.checklist`) | Schemas exist, manual validation only (Ledger L-007) |
| **Feedback loop** | `/speckit.converge` — assess codebase against spec, append remaining work | Three-part exit (accepted / repair / change order) but no convergence assessment |
| **Customization** | Layered resolution: project-local → presets → extensions → core | Profiles (F-013) exist but no override hierarchy |
| **Constitution** | Generated artifact, template-driven | North-star artifact, human-amended |

**What we can take:** not the CLI, but the **structural patterns** — explicit gates, consistency checks, convergence assessment, and layered scoping — all expressible in markdown and schemas.

---

## 1. Add a Clarify Gate to the Work-Order Lifecycle

Spec Kit's `/speckit.clarify` is the most actionable spec-debt tool: it asks clarifying questions, identifies edge cases, and flags underspecified areas *before* planning begins. The research paper on specification debt and the "agent configuration smells" paper both confirm this is a real, measurable problem.

### Proposal

Add an optional `clarify` state to the work-order state machine, between `draft → released`:

```
draft → clarify → released → in-progress → ...
```

A `clarify` gate means: before an order can be `released`, an agent (or human) runs the spec through a structured set of probes and records what was surfaced and resolved.

### What this looks like

A new `clarify` section in the work-order template:

```yaml
# (existing frontmatter)

clarify:
  ran: true
  probes:
    - question: "What happens when the database is unreachable?"
      resolution: "Graceful degradation — show cached data + retry indicator"
      status: resolved
    - question: "Is the search scope bounded? How many results max?"
      resolution: "Top 25 results, pagination handles the rest"
      status: resolved
    - question: "What's the fallback when the AI model returns an empty response?"
      resolution: "Default template with 'no results found' message"
      status: deferred -> ledger
  unresolved_found: false
```

Or optionally, a standalone `clarify-<order-id>.md` artifact for more thorough treatment.

### Value

- Forces spec-debt resolution **before** a builder picks up the order
- Produces a record of what was considered and resolved
- Feeds the ledger with deferred questions
- Directly addresses the "specification debt" concern from the charter

### Weight: Light

- No schema changes (adds optional field to work-order frontmatter)
- Optional gate — orders that don't need it can skip
- Human runs it until automation is warranted

---

## 2. Cross-Artifact Consistency Checks (Lightweight Analyze)

Spec Kit's `/speckit.analyze` checks for consistency and coverage across specs, plans, and tasks. kst_promptware has schemas but no automated consistency validation (per Ledger L-007: "v0.1 schema validation is a one-off manual run recorded in evidence blocks").

### Proposal

Define a **consistency checklist** that's run (manually or via agent) before an order moves from `review → complete`. The checklist lives as a playbook or a section of the existing templates:

```markdown
## Consistency Check (run before review → complete)

- [ ] Every artifact referenced in `writes` exists on disk
- [ ] Every acceptance check is testable (not subjective)
- [ ] Feature/fix frontmatter matches the capability map status
- [ ] No `contentId` or `id` collisions with existing artifacts
- [ ] Tech stack decisions are documented (if not covered by a contract)
- [ ] Ledger entries that were resolved are marked resolved
```

This is **already possible** — the ledgers, schemas, and work-order template support it. The missing piece is a canonical checklists document.

### Value

- Moves from "we validate schemas manually" to "we validate schemas + cross-references manually"
- Prevents the "configuration smells" pattern (context bloat, blind references) that the June 2026 paper identified
- Composable: each project adds its own checks

### Weight: Light

- A single markdown file (`playbooks/consistency-check.md`)
- No code, no CLI, no schema change
- The checklist itself is versioned and evolved

---

## 3. Convergence Assessment (Close the Feedback Loop)

Spec Kit's `/speckit.converge` assesses the codebase against the spec, plan, and tasks, then appends remaining work. kst_promptware's three-part exit is good but doesn't have an explicit "how much of the spec is actually implemented" assessment at closeout.

### Proposal

Add a **convergence section** to the work-order `reconciliation` block — the final step before `complete → reconciled`:

```yaml
reconciliation:
  convergence:
    assessment: "10/12 acceptance checks pass. Two fail due to spec gap."
    remaining_work:
      - item: "Offline fallback for API calls"
        action: change_order
    evidence_key: "convergence-report-WO-0008"
```

For thorough convergence, a companion artifact `convergence-<order-id>.md` can capture the assessment in detail.

### Value

- Makes the three-part exit concrete: was this a repair loop or a change order?
- Prevents "the spec says it's done but the code doesn't match" drift
- Feeds directly into the capability map's status derivation
- Distinguishes between "acceptance checks pass but it doesn't feel done" (per §9.3: record concern and proceed) vs actual convergence failure

### Weight: Light

- Optional block in the existing reconciliation section
- No state machine changes (convergence is a closeout step, not a new state)
- The convergence artifact template is a new template file

---

## 4. Adapter Shim Patterns from Spec Kit's Integration Architecture

Spec Kit's integration system is the most mature pattern in the spec-first space for multi-agent support. It has:

- A base class hierarchy (`IntegrationBase → MarkdownIntegration / TomlIntegration / YamlIntegration / SkillsIntegration`)
- A registry pattern (`INTEGRATION_REGISTRY` building from integration subpackages)
- Context file support per agent (`CLAUDE.md`, `AGENTS.md`, `.github/copilot-instructions.md`, etc.)
- Skill installation vs. slash-command prompts vs. config-file install modes

kst_promptware already has adapter shims queued (F-007) with the restriction "pointers only, one screen max."

### Proposal

When F-007 is taken up, model the shims on Spec Kit's **three-mode installation pattern**:

| Installation mode | Agent type | kst_promptware artifact | Example |
|---|---|---|---|
| **Context file** | Claude, Copilot, Cursor | Copy relevant sections of `constitution.md` + agent profile into `CLAUDE.md` / `AGENTS.md` | `agents/planner.md` → `CLAUDE.md` |
| **Skill directory** | Codex, Roo, Cline | Copy `skills/<name>/SKILL.md` into `.agents/skills/<name>/` | `skills/work-order-intake/SKILL.md` |
| **Rules file** | Cursor, Windsurf | Copy `rules/common/*.md` into `.cursor/rules/` or `.windsurf/rules/` | `rules/common/testing.md` |

The key insight from Spec Kit: **one artifact shape, multiple agent-specific install paths.** The shim should be a simple copier, not a generator.

### Value

- Fills the largest gap in kst_promptware's library layer (F-007 has been queued since v0.1 bootstrap)
- Makes the bundle truly portable across tools
- Low friction: a README with copy-paste instructions is enough for v0.2

### Weight: Medium

- Requires the shim templates to be written
- No runtime or build step — just markdown copy instructions
- Could live as a `shims/` directory with one file per agent type

---

## 5. Validation Playbook (Making L-007 Actionable)

Ledger L-007 explicitly defers automated gatekeeping to a later maturity tier. But the research is clear: **lint leakage in 62% of sampled AGENTS.md files** (June 2026 config smells paper). The spec layer becomes its own debt source without validation.

### Proposal

Rather than waiting for automation, write a **validation playbook** — a structured, manually-executable checklist for validating governed artifacts at `review → complete`. Modeled on Spec Kit's `/speckit.checklist`:

```markdown
## Field-Level Validation (WO frontmatter)

- [ ] `schema` field matches the canonical schema id
- [ ] `status` is a legal state machine value
- [ ] `acceptance_checks` have clear pass/fail criteria (not "works well")
- [ ] `writes` paths are relative, not absolute
- [ ] No fields contain placeholder text ("TODO", "FIXME")

## Cross-Artifact Validation

- [ ] Every `id` used in reads/writes exists as a file or section
- [ ] Agent definitions referenced by tier exist in `agents/`
- [ ] Skill references point to valid skill directories
- [ ] No artifact exceeds the governance header convention

## Markdown Hygiene

- [ ] No bare `Math.random()` patterns in reference code
- [ ] All external links are HTTPS
- [ ] Governance headers are present on every governed artifact
- [ ] `ledger.md` has no unresolved entries that block this order
```

### Value

- Closes L-007 with a tractable manual step that can become automated later
- Prevents the "configuration smells" pattern heisenbug where poorly-structured agent context degrades task success rates
- The checklist can be run by an agent as a pre-closeout step

### Weight: Light

- Single markdown file (`playbooks/validation-checklist.md`)
- No code, no schema change
- CAN be automated later as a GitHub Action or pre-commit hook

---

## 6. Layered Override System (Profiles 2.0)

Spec Kit's layered resolution (project-local → presets → extensions → core) is elegant. kst_promptware's profiles (F-013) define what to install, but not how to override when a downstream project needs to customize.

### Proposal

Extend the profile concept with an **override resolution order** documented as a convention:

```
1. Project-local overrides    (.kst/overrides/ — NOT in the bundle)
2. Active profile             (profiles/<name>.md)
3. Bundle defaults            (the repo's files themselves)
```

When `profiles/minimal.md` says "use `profiles/full.md`" for a specific rule, that's a profile-level override. When `profiles/full.md` says "inherit from bundle defaults," that's the fallback.

Document this in a `ORDER_OF_OPERATIONS.md` or as a section in the profiles README.

### Value

- Downstream projects can customize without forking
- Maintains the "no build step, no runtime" principle
- Profiles become composable, not just installation manifests

### Weight: Light

- Documentation only — one file or one README section
- The convention can be validated by the human gate

---

## Summary Table

| # | Proposal | Weight | Where it lands | Depends on |
|---|---|---|---|---|
| 1 | Clarify gate | 🟢 Light | `templates/work-order.md` + optional playbook | Constitution §8 amendment (optional gate) |
| 2 | Consistency checklist | 🟢 Light | `playbooks/consistency-check.md` | — |
| 3 | Convergence assessment | 🟢 Light | `templates/work-order.md` reconciliation block | — |
| 4 | Adapter shim patterns | 🟡 Medium | `shims/<agent>.md` | F-007 unblocking |
| 5 | Validation playbook | 🟢 Light | `playbooks/validation-checklist.md` | L-007 resolution |
| 6 | Override hierarchy docs | 🟢 Light | `profiles/ORDER_OF_OPERATIONS.md` | — |

**Total weight:** 5 light + 1 medium. No runtime, no build step, no new dependencies.

---

## Appendix: The Constitution Amendment (Clarify Gate)

If proposal #1 is accepted, the constitution's §8 state machine needs an additional transition:

`draft → clarify` (add to §8.1)
`clarify → released` (standard release)
`clarify → draft` (clarify revealed the spec needs rework)

And §8.4 changes from:

> `draft → released` and `review → complete` require the order's declared gate

to:

> `draft → clarify`, `clarify → released`, and `review → complete` require the order's declared gate

This is a **lightweight amendment** — additive to the state machine, not restructuring anything.

---

## References

- **Spec Kit README** — <https://github.com/github/spec-kit>
- **Spec Kit SDD philosophy** — `spec-driven.md` (specification as primary artifact, power inversion)
- **Promptware Engineering** (arXiv:2503.02400) — ACM TOSEM 2026, treating prompts as first-class SE artifacts
- **Agent Configuration Smells** (June 2026) — lint leakage in 62% of AGENTS.md files; context bloat in 42%
- **Specification Debt / Intent Debt** — Margaret-Anne Storey's Triple Debt Model (technical / cognitive / intent)
- **kst_promptware Ledger** — L-007 (validator deferred), L-008 (v3 integration), L-011 (library coverage gap)
- **kst_promptware Capability Map** — F-007 (adapter shims, queued), F-013 (profiles, delivered)