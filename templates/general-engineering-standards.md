# General Engineering Standards

**Status:** Active baseline
**Last Verified:** 2026-05-25
**Applies to:** Web-based projects (any framework). Adapt the specifics, keep the principles.

<!-- STALE CHECK: if Last Verified is more than 6 months old, treat this doc as advisory and verify each section against current practice before relying on it. -->

A skimmable reference. Sections are independent — jump to what you need.

---

## 1. Data-driven design

Separate **logic** (rendering, validation, calculations) from **data** (configuration, content, business rules).

- Behavior changes without touching code
- Non-engineers can contribute content
- Easier to test

Conventional locations: `data/`, `config/`, `data/schemas/`.

---

## 2. Code organization

**One responsibility per file.** File name matches class/component name. PascalCase for classes (`UserProfile.js`), kebab-case for utilities (`api-client.js`), lowercase for entry points (`main.js`).

**Project layout (typical):**
```
src/
├── pages/         # Routes
├── components/    # Reusable UI
├── services/      # Business logic, API calls
├── utils/         # Pure helpers
└── main.js
public/            # Static assets, JSON data
```

**Separation of concerns:**
- Pages compose; they don't hold business logic.
- Components own local state; they don't reach into global state directly.
- Services own shared resources; they don't depend on UI.

---

## 3. Naming conventions

| Thing | Convention | Example |
|-------|-----------|---------|
| Variables, functions | camelCase | `playerHealth`, `calculateDamage` |
| Classes, components | PascalCase | `UserProfile`, `DataManager` |
| Constants | SCREAMING_SNAKE_CASE | `API_BASE_URL`, `MAX_RETRIES` |
| Private members | `_` prefix | `_internalState` |
| Booleans | `is/has/can/should` prefix | `isAuthenticated`, `hasPermission` |
| Component files | PascalCase | `UserProfile.tsx` |
| Utility files | kebab-case | `api-client.ts` |
| HTML/CSS files | kebab-case | `debug-panel.html` |
| JSON data | kebab-case | `user-config.json` |
| Directories | lowercase, singular | `component/`, `service/` |

**Assets:** hierarchical, descriptive — `icon-user-small`, `banner-hero-mobile`, `config-production`.

---

## 4. Documentation in code

**Default: write no comments.** Names should carry meaning. Add a comment only when the WHY is non-obvious — a constraint, a workaround, behavior that would surprise a reader.

```javascript
// ✅ Explains a non-obvious constraint
// Clamp velocity to prevent tunneling through walls
if (velocity > MAX_VELOCITY) velocity = MAX_VELOCITY;

// ❌ Restates the code
// Set velocity to max
velocity = MAX_VELOCITY;
```

**File headers** are optional. If included, keep them to a one-line purpose statement plus any non-obvious interactions. Avoid multi-paragraph docstrings.

**Markers:** `TODO`, `FIXME`, `HACK`, `NOTE` — searchable, scoped to the relevant line.

---

## 5. Performance

Apply when measurement shows you need to. Don't pre-optimize.

- **Memoize** expensive renders only after profiling identifies them.
- **Lazy-load** images and routes below the fold.
- **Batch** API requests instead of looping individual calls.
- **Paginate / virtualize** lists over ~100 visible items.
- **Debounce** search inputs and other rapid-fire events.

When in doubt: ship it, measure, then optimize the hot path.

---

## 6. Testing & validation

- Each feature should have a manual test checklist for the golden path and the obvious edge cases.
- Build **isolated test pages** (component playground, API viewer) — fastest iteration loop.
- Use prefixed console logs: `[Auth]`, `[API]`, `[Validation]`. Gate verbose logs behind a debug flag.
- Browser DevTools (breakpoints, performance profile, network tab) is your primary tool.

---

## 7. Version control

**Branch model:** `main` is stable. Work on feature branches. See [`guides/contributing.md`](../guides/contributing.md) for the canonical git policy.

**Commit cadence:** small, atomic, working code. One logical change per commit.

### Commit message format

Use **Conventional Commits** (lowercase). This is the canonical format for this repository — it matches actual git history and integrates with standard tooling.

```
type(scope): short description

Optional body: explain WHY, not WHAT. Wrap at 72 chars.
Reference issues with: Closes #123
```

**Types:** `feat`, `fix`, `docs`, `refactor`, `perf`, `test`, `chore`, `style`, `build`, `ci`.

**Scope** is optional but useful for monorepos or multi-area changes.

**Examples:**
```
feat(inventory): add player pickup collision

- Inventory.add/remove with capacity check
- UI overlay re-renders on item change
```

```
fix(physics): prevent tunneling at high velocity

Collision detection now uses swept AABB instead of point check.
Closes #42
```

```
docs(workflow): document cadence rules
```

**Do not include:**
- A `Files Touched:` list — `git show --stat` already tracks that. Forcing the author to duplicate it just rots.
- Generated co-author signatures unless the user has explicitly asked for them.

### What to commit

**Include:** source, docs, config, small assets.
**Exclude (`.gitignore`):** `node_modules/`, build artifacts, OS files (`.DS_Store`), IDE files, large binaries (use Git LFS), temp files.

---

## 8. Debugging & DevTools

- Build **debug overlays** for runtime state when the app is non-trivial (FPS, memory, active connections). Toggle with a hotkey.
- Expose a **`window.DEBUG`** namespace in dev builds with helpers (set user, clear cache, toggle flags).
- **Graceful failures** at system boundaries: try/catch around fetches, fallback to known-good defaults rather than crashing.
- **Validate at boundaries** (user input, external APIs). Trust internal code.

---

## 9. Code clarity principles

These cost nothing and pay back forever.

- **Readable over clever.** A 6-line `if/else` beats a chained ternary.
- **Named constants instead of magic numbers.** `LOW_HEALTH_THRESHOLD = 20` beats `if (health < 20)`.
- **DRY when it's actually duplicated logic** — not when three lines happen to look similar.
- **Early returns / guard clauses** reduce nesting and make happy-path obvious.
- **Incremental development:** write minimal code → verify → commit → expand. Avoid writing 300 lines before the first run.

---

## 10. Pre-merge checklist

Before opening a PR or merging:

- [ ] Naming conventions followed
- [ ] No debug logs in production paths
- [ ] Manual happy path + obvious edge cases tested
- [ ] No new console errors
- [ ] Commit messages follow Conventional Commits
- [ ] Cross-references / docs updated if behavior changed

---

## Resources

- **MDN Web Docs** — https://developer.mozilla.org/
- **web.dev** — https://web.dev/
- **Conventional Commits spec** — https://www.conventionalcommits.org/
