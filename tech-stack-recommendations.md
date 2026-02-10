# Tech Stack Recommendations

General technical guidance for game prototyping projects. This document helps AI agents and team members make consistent technology decisions that reduce friction and increase interoperability across projects.

**Audience:** AI agents (primary), human developers (secondary)
**Scope:** Shared pipeline concerns, engine selection, project structure, and tooling
**Philosophy:** Recommendations with reasoning, not mandates. The goal is to front-load engineering decisions so the team can focus on building game experiences.

---

## Table of Contents

1. [Engine Selection](#1-engine-selection)
2. [Language and Type System](#2-language-and-type-system)
3. [Build Tooling](#3-build-tooling)
4. [Project Structure](#4-project-structure)
5. [Data-Driven Architecture](#5-data-driven-architecture)
6. [Asset Pipeline](#6-asset-pipeline)
7. [Shared Code and Tooling](#7-shared-code-and-tooling)
8. [State Management](#8-state-management)
9. [Deployment and Packaging](#9-deployment-and-packaging)
10. [Version Control](#10-version-control)
11. [Optional Considerations](#11-optional-considerations)
12. [Decision Quick Reference](#12-decision-quick-reference)

---

## 1. Engine Selection

### Recommended Engines

**Phaser 4** — Web-first games

Use Phaser when the primary distribution target is a web browser. Phaser produces lightweight builds (sub-1MB is achievable), loads instantly, and works well on mobile browsers. The framework is written natively in TypeScript, has excellent tooling through Vite, and provides built-in support for JSON data loading, sprite sheets, texture atlases, and Tiled tilemaps.

Phaser 4 RC5 is production-stable. Install via `npm install phaser@beta` (the formal stable tag has not yet landed as of February 2026, but the Phaser team has declared RC5 the final release candidate).

**Godot 4.5** — Local-first / desktop-first games

Use Godot when the game needs native desktop performance, complex built-in systems (physics, particles, navigation meshes, animation state machines), or a visual scene editor. Godot exports to native executables at ~50MB — significantly smaller than Electron-wrapped web games. Its primary language is GDScript (Python-like, easy to learn).

Godot can export to web via WebAssembly, but the payload is heavy (30-40MB) and mobile browser performance is poor. If web distribution matters, prefer Phaser.

### Engines Not Recommended for Current Projects

- **GDevelop** — Visual scripting focus limits flexibility for data-driven workflows
- **Unity** — Heavyweight for 2D prototyping, licensing complexity
- **Unreal** — Overkill for 2D game prototyping

### When to Choose Which

| Factor | Phaser | Godot |
|--------|--------|-------|
| Primary target | Web browser | Desktop application |
| Bundle size (web) | < 1MB | 30-40MB (WASM) |
| Language | TypeScript / JavaScript | GDScript |
| Visual editor | None (code-only) | Full scene editor |
| Built-in physics | Basic (Arcade, Matter.js) | Full (Jolt Physics in 4.4+) |
| Tilemap editing | External (Tiled) | Built-in TileMap editor |
| Team skill match | Strong (JS/TS is known) | Moderate (GDScript is easy to learn) |
| AI agent support | Excellent (standard web toolchain) | Good (text-based files, but GDScript is less common in training data) |

---

## 2. Language and Type System

### For Phaser Projects: TypeScript (recommended) or JavaScript

TypeScript is the default recommendation. It provides meaningful autocomplete with Phaser's large API surface, catches real bugs at compile time, and enables better AI agent code generation (type context helps agents make fewer mistakes).

**When plain JavaScript is acceptable:**
- One-off prototypes that will not be maintained
- Rapid jam-style development where setup friction matters
- Team members who are blocked by TypeScript configuration issues

If starting with JavaScript, structure the project so it can migrate to TypeScript later (use ES modules, avoid patterns that fight type inference).

**TypeScript configuration for prototyping:**

Use a relaxed config that provides value without friction. Strict mode can be enabled later if the project graduates from prototype status.

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": false,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  },
  "include": ["src"]
}
```

The key setting is `"strict": false` — this gives you type inference and autocomplete without requiring explicit types everywhere. Turn on `"strict": true` when the project stabilizes.

### For Godot Projects: GDScript

GDScript is the primary language for Godot. It has optional static typing (use it when convenient), integrates tightly with the editor, and is well-documented.

C# is supported in Godot but does not work with web export. Avoid it unless the project will never target web.

A community project (GodotJS) provides TypeScript bindings for Godot, but it is not officially supported. Do not depend on it for production.

---

## 3. Build Tooling

### Phaser Projects: Vite

Vite is the standard build tool for Phaser projects. It is what the Phaser team uses in their official templates and tutorials.

**Scaffold a new project:**

```bash
npm create @phaserjs/game@latest my-game
# Select: Vite + TypeScript (no framework)
cd my-game
npm install
npm run dev
```

This gives you hot-reload, TypeScript compilation, and production builds out of the box.

**Key scripts:**
- `npm run dev` — Development server with hot-reload
- `npm run build` — Production build to `dist/`

### Godot Projects

Godot has its own build system. No external build tooling is needed. Export templates are managed through the Godot editor's export dialog.

### Package Manager: npm (default)

Use **npm** unless the team has a specific reason to use something else. It is what Phaser's documentation uses, requires no additional installation, and is the simplest option for a team with mixed technical skill levels.

pnpm is a valid alternative if a team member prefers it — all Phaser tooling supports it. But do not introduce it just for marginal performance gains if it creates onboarding friction.

---

## 4. Project Structure

### Phaser Project Template

See [phaser-project-template.md](phaser-project-template.md) for a complete starter template with directory layout, file conventions, and example files.

The key structural conventions:

```
my-game/
├── public/
│   └── assets/              # Static assets (images, audio, tilemaps)
│       ├── sprites/
│       ├── audio/
│       ├── maps/
│       └── fonts/
├── src/
│   ├── main.ts              # Application entry point
│   ├── game/
│   │   ├── config.ts        # Phaser GameConfig
│   │   ├── scenes/          # One file per scene
│   │   │   ├── BootScene.ts
│   │   │   ├── PreloadScene.ts
│   │   │   ├── MenuScene.ts
│   │   │   └── GameScene.ts
│   │   ├── objects/         # Game object classes (players, enemies, items)
│   │   └── systems/         # Game systems (collision, scoring, spawning)
│   ├── data/                # JSON game data (entity defs, level configs)
│   └── utils/               # Shared helpers
├── data/                    # JSON sidecar files (entity definitions, configs)
├── package.json
├── tsconfig.json
├── vite.config.ts
└── index.html
```

**Critical conventions:**
- **One scene per file** in `src/game/scenes/`
- **Game data lives in JSON**, not hardcoded in game logic (see [Data-Driven Architecture](#5-data-driven-architecture))
- **Assets in `public/assets/`** — Vite serves these statically
- **No god objects** — break game logic into focused systems and objects

### Godot Project Structure

```
my-game/
├── project.godot             # Project configuration
├── addons/                   # Third-party plugins
├── assets/                   # Raw assets
│   ├── sprites/
│   ├── audio/
│   └── fonts/
├── data/                     # JSON data files (entity definitions, configs)
├── scenes/                   # .tscn scene files
│   ├── levels/
│   ├── characters/
│   └── ui/
├── scripts/                  # GDScript files
│   ├── autoload/             # Singleton managers
│   ├── components/           # Reusable behaviors
│   └── systems/              # Game systems
├── resources/                # .tres resource files
└── export_presets.cfg        # Export configurations
```

**Godot conventions:**
- Use **snake_case** for all file and folder names
- Keep a `data/` directory for JSON files that could be shared with external tools
- Register global managers as Autoload singletons in Project Settings

---

## 5. Data-Driven Architecture

This is the most important architectural pattern for interoperability. **Separate game data from game logic.**

### Core Principle

Game entities, level layouts, item stats, dialog trees, and configuration should be defined in **JSON files**, not hardcoded in game scripts. Game code reads these definitions at runtime.

This enables:
- **Reuse** — Entity definitions from one game can be used in another
- **Non-programmer editing** — Team members can modify game content by editing JSON
- **Tool integration** — External editors (like EntityManager) can produce data that games consume
- **AI agent efficiency** — Agents can modify game content without touching game logic

### JSON Sidecar Pattern

Store game data in a `data/` directory as JSON files alongside (or within) the project:

```
data/
├── entities/
│   ├── player.json
│   ├── enemy-slime.json
│   └── item-health-potion.json
├── levels/
│   ├── level-01.json
│   └── level-02.json
└── config/
    ├── game-settings.json
    └── difficulty.json
```

### Entity Definition Format

For maximum interoperability, entity definitions should follow a consistent structure. This format is compatible with import/export to the EntityManager and GDM toolchain if the team adopts those tools later.

**Minimum viable entity definition:**

```json
{
  "id": "player",
  "name": "Player",
  "category": "character",
  "sprite": {
    "source": "assets/sprites/player.png",
    "frameWidth": 32,
    "frameHeight": 32
  },
  "animations": [
    {
      "key": "idle",
      "frames": { "start": 0, "end": 3 },
      "frameRate": 8,
      "loop": true
    },
    {
      "key": "walk",
      "frames": { "start": 4, "end": 11 },
      "frameRate": 12,
      "loop": true
    }
  ],
  "properties": {
    "health": 100,
    "speed": 120,
    "damage": 10
  }
}
```

**Guidelines for entity definitions:**
- Use a flat, readable structure — avoid deep nesting
- Include an `id` field that can serve as a lookup key
- Keep gameplay properties in a `properties` object
- Reference assets by relative path
- Avoid engine-specific data in the definition (handle engine mapping in game code)

### Loading Data in Phaser

```typescript
// In PreloadScene
preload() {
  this.load.json('player-data', 'data/entities/player.json');
  this.load.json('level-data', 'data/levels/level-01.json');
}

// In GameScene
create() {
  const playerData = this.cache.json.get('player-data');
  const player = new Player(this, 100, 100, playerData);
}
```

### Loading Data in Godot

```gdscript
# Load entity definition from JSON
func load_entity(path: String) -> Dictionary:
    var file = FileAccess.open(path, FileAccess.READ)
    var json = JSON.new()
    json.parse(file.get_as_text())
    return json.data
```

### What NOT to Hardcode

- Entity stats (health, speed, damage)
- Level layouts and spawn points
- Item definitions and drop tables
- Dialog text
- Difficulty curves and balance values
- UI text and labels

### What IS Appropriate in Code

- Game mechanics and logic (how damage is calculated, not how much)
- Physics behavior (how movement works, not movement speed)
- Scene flow (what happens when a level loads)
- Input handling
- Rendering logic

---

## 6. Asset Pipeline

### Sprite Sheets and Atlases

**Recommended workflow:**
1. Create sprites as individual frames or organized sheets in your art tool
2. Use a texture packer to combine them into an atlas (JSON + PNG)
3. Place the atlas in `public/assets/sprites/` (Phaser) or `assets/sprites/` (Godot)

**Texture packing tools:**
- [TexturePacker](https://www.codeandweb.com/texturepacker) — Industry standard, paid
- [FreeTexturePacker](http://free-tex-packer.com/) — Free alternative
- [ShoeBox](https://renderhjs.net/shoebox/) — Free, Adobe AIR-based

For Phaser, export atlases in **JSON Hash** or **JSON Array** format.

### Tilemaps

**Recommended tool:** [Tiled](https://www.mapeditor.org/) (free, cross-platform)

**For Phaser:**
- Export from Tiled as **JSON format** (not TMX)
- Load with `this.load.tilemapTiledJSON()`
- Embed tilesets in the tilemap JSON for simplicity

**For Godot:**
- Use the built-in TileMap editor for simple maps
- For complex maps, Tiled exports can be imported via community plugins

### Audio

- Use **OGG Vorbis** (.ogg) as the primary format for web — good compression, widely supported
- Keep **WAV** source files for archival
- MP3 is acceptable as a fallback but OGG is preferred for Phaser

### File Naming

Use **kebab-case** for all asset files:
- `player-idle.png`
- `enemy-slime-walk.png`
- `level-01-tilemap.json`
- `bgm-main-theme.ogg`

---

## 7. Shared Code and Tooling

### Shared Utilities Repository

Maintain a separate git repository for code and utilities shared across projects. Teams can clone or download this repo into their project as needed.

**Repository structure:**

```
shared-game-utils/
├── src/
│   ├── data-loader.ts       # Generic JSON entity/config loader
│   ├── scene-helpers.ts     # Common scene setup patterns
│   ├── math-utils.ts        # Vector math, random ranges, etc.
│   └── types/
│       └── entity.ts        # Shared entity type definitions
├── package.json
├── tsconfig.json
└── README.md
```

**Usage in a project:**

```bash
# Clone into project (one-time)
git clone https://github.com/your-org/shared-game-utils.git src/shared

# Or add as a git submodule
git submodule add https://github.com/your-org/shared-game-utils.git src/shared
```

**Guidelines:**
- Keep it small and focused — only include genuinely reusable code
- Do not put engine-specific code in the shared repo (keep it engine-agnostic where possible)
- If a utility only makes sense for Phaser, put it in a `phaser/` subdirectory
- Type definitions for shared data formats (entity definitions, level configs) are high-value candidates for sharing
- This may evolve into an npm package over time, but a git repo is sufficient to start

### EntityManager and GDM

The team has access to two specialized tools:

- **EntityManager** — A web-based visual editor for creating and managing entity definitions with sprite sheet support, animation timelines, and hitbox editing
- **GDM (Game Data Management)** — A schema-first validation and compilation pipeline that aggregates distributed JSON entity files into runtime manifests

These tools are **optional**. They are most useful when a project has matured beyond the prototype stage and the team wants a structured content pipeline. For early prototyping, the JSON sidecar pattern described in [Data-Driven Architecture](#5-data-driven-architecture) is sufficient and compatible with later adoption of these tools.

---

## 8. State Management

### Phaser Projects

**For prototypes:** Use Phaser's built-in `this.registry` for global state (score, player health, settings) and scene `init(data)` for passing data between scenes. This requires zero setup.

```typescript
// Set global state
this.registry.set('score', 0);

// Read from any scene
const score = this.registry.get('score');

// React to changes
this.registry.events.on('changedata-score', (parent, value) => {
  this.scoreText.setText(`Score: ${value}`);
});
```

**For larger projects:** Create a dedicated `GameState` singleton class that holds player progression, inventory, and session data. Access it from any scene via `this.game`.

**For character/AI behavior:** Use the State Machine pattern — encapsulate each state (idle, running, attacking) as an object with `enter()`, `update()`, `exit()` methods.

### Godot Projects

Use Autoload singletons for global state. Register a `GameManager.gd` script as an Autoload in Project Settings. It persists across scene changes and is accessible from any node.

---

## 9. Deployment and Packaging

### Web Deployment (Phaser)

```bash
npm run build
```

This produces a `dist/` folder containing static files. Deploy to any static hosting:

| Platform | Cost | Notes |
|----------|------|-------|
| [itch.io](https://itch.io) | Free | Best for game prototypes, built-in community |
| [Netlify](https://netlify.com) | Free tier | Auto-deploy from git, custom domains |
| [GitHub Pages](https://pages.github.com) | Free | Simple, tied to repo |
| VPS | Varies | Full control, team already has capacity |

### Desktop Packaging (Phaser → Desktop)

**Recommended: Tauri**

Tauri wraps web games in a native shell using the OS's built-in webview. It produces dramatically smaller bundles than Electron (3-10MB vs 100MB+), launches faster, and uses less memory.

Phaser provides an official Tauri template:
```bash
# When starting a new project with desktop target
npm create @phaserjs/game@latest my-game
# Select Tauri template
```

**Fallback: Electron**

Use Electron if Tauri's OS webview produces inconsistent rendering on target platforms, or if the team needs Steam distribution (Electron has a more proven Steam deployment path).

### Desktop Deployment (Godot)

Godot exports to native executables directly — no wrapper needed. Use the Export dialog in the editor. Native Godot builds are ~50MB, render via Vulkan/OpenGL, and perform better than any web-wrapper approach.

---

## 10. Version Control

### Git Conventions

Every project should have a git repository. Use the branching and commit conventions from [Sandbox/contributing.md](Sandbox/contributing.md).

### .gitignore Essentials

**Phaser projects:**
```gitignore
node_modules/
dist/
.vite/
*.local
.DS_Store
```

**Godot projects:**
```gitignore
.godot/
*.import
.DS_Store
```

### Large Assets

If a project includes many large binary files (PSD sources, high-res audio, video), consider using **Git LFS** to keep the repository manageable. For most prototype projects with web-optimized assets, standard Git is fine.

---

## 11. Optional Considerations

These are patterns worth considering as a project matures, but are not required for prototyping.

### Content Packs

For games with moddable or expandable content, consider a layered content system where a "base" set of entity definitions can be overridden or extended by additional packs. The GDM game-backend implements this pattern. It is not necessary for most prototypes.

### ECS (Entity-Component-System) Architecture

For games with many entity types that share behaviors in different combinations, an ECS pattern (where entities are composed of reusable components like Transform, Sprite, Collider, Health) provides flexibility. The GDM schema uses this model. For simpler games, a class hierarchy or flat JSON properties are fine.

### Automated Testing

For prototypes, manual playtesting is usually sufficient. If a project stabilizes and will be maintained, consider adding:
- **Vitest** for unit tests on game logic (Phaser projects)
- **Playwright** for automated UI/integration tests
- Godot has a built-in testing framework for GDScript

### CI/CD

Not necessary for prototyping. When a project is ready for regular deployment, GitHub Actions can automate builds and deployment. The Phaser + Vite build is simple enough to run in any CI environment.

---

## 12. Decision Quick Reference

A summary for fast decision-making:

| Decision | Default Choice | When to Deviate |
|----------|---------------|-----------------|
| Engine (web) | Phaser 4 | — |
| Engine (desktop) | Godot 4.5 | Use Phaser+Tauri if the game started as web |
| Language (Phaser) | TypeScript | Plain JS for throwaway prototypes |
| Language (Godot) | GDScript | — |
| Build tool | Vite | — |
| Package manager | npm | pnpm if preferred, both work |
| Data format | JSON sidecar files | — |
| Tilemap editor | Tiled | Godot built-in for Godot-only projects |
| Texture packing | TexturePacker or FreeTexturePacker | Individual sprites fine for prototypes |
| State management | Phaser registry / Godot Autoload | Dedicated state class for larger projects |
| Web hosting | itch.io or Netlify | VPS if team needs custom server |
| Desktop packaging | Tauri (for Phaser games) | Electron for Steam, Godot native for Godot |
| Shared code | Git repo cloned into project | npm package if it matures |
| Entity definitions | JSON with id, sprite, properties | ECS components if complexity warrants it |

---

## Related Documents

- [phaser-project-template.md](phaser-project-template.md) — Starter template for new Phaser projects
- [Sandbox/contributing.md](Sandbox/contributing.md) — Git workflow and branching conventions
- [.docs/templates/General Engineering Standards.md](.docs/templates/General%20Engineering%20Standards.md) — Code organization and naming standards
