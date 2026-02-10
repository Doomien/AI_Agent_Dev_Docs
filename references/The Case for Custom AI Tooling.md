# The Case for Custom API Tooling for Agentic Software Development

## The Interface Segregation Principle for Human-AI Collaboration

As AI agents become integral to software development workflows, we're discovering a fundamental mismatch: the tools optimized for human interaction are often poorly suited for AI agents, and vice versa.

## The Problem with Current Approaches

**Agents Using Human Interfaces**
When AI agents interact with visual editors, they face unnecessary friction:
- Parsing large GUI-generated files (Unity scenes, Phaser Editor configs) to modify small sections
- Maintaining context across massive JSON or XML structures
- Navigating abstractions designed for visual exploration, not programmatic access
- Fighting with tools that separate code from visual state

**Humans Using Agent-Generated Code**
Conversely, purely code-first approaches create challenges for humans:
- Losing spatial reasoning and visual feedback
- Slower iteration on layout and design
- Difficulty exploring possibilities and experimenting
- Steeper learning curves for non-programmers

**The Traditional Compromise**
Most workflows force a choice: use the GUI OR work with agents, but switching between them creates synchronization problems and duplicated state.

## A Better Model: Interface Segregation

The solution is to provide **different interfaces optimized for different actors**, while maintaining a **single source of truth**:

```
┌─────────────────────────────────────────────┐
│         Single Source of Truth              │
│         (Structured Data: JSON/DB)          │
└─────────────────┬───────────────────────────┘
                  │
        ┌─────────┴─────────┐
        │                   │
┌───────▼────────┐  ┌────-──▼──────────┐
│ Human Interface│  │  Agent Interface │
│                │  │                  │
│  - GUI Editor  │  │  - API Endpoints │
│  - Visual Tools│  │  - MCP Server    │
│  - Drag & Drop │  │  - Query/Update  │
│  - WYSIWYG     │  │  - Validation    │
└────────────────┘  └──────────────────┘
```

## Implementation: Custom API Layers

Rather than agents directly editing files, expose purpose-built APIs:

### Example: Game Development with GDevelop

**Human Workflow:**
- Open GDevelop visual editor
- Design levels spatially
- Place objects via drag-and-drop
- Configure behaviors through forms

**Agent Workflow (via API/MCP):**
```javascript
// Agent queries only what it needs
GET /api/object?name=Player&scene=Level1
// Returns 50 lines instead of 10,000

// Agent makes targeted update
POST /api/object/update
{
  "name": "Player",
  "scene": "Level1", 
  "behaviors": {
    "PlatformerBehavior": {
      "jumpSpeed": 600  // Was 500
    }
  }
}

// Agent adds new event logic
POST /api/events/add
{
  "scene": "Level1",
  "group": "Player Movement",
  "condition": "Key Space is pressed",
  "action": "Set Player.doubleJumpAvailable to False"
}
```

**Benefits:**
- Agent context stays manageable (<1000 tokens per operation)
- API validates changes before applying
- Both human and agent see changes immediately
- No file format conflicts or merge issues
- Version control tracks meaningful changes, not GUI artifacts

## Key Design Principles

### 1. Query-First Architecture
Agents should query for summaries/indexes before retrieving full data:
```javascript
GET /api/index  // Returns project structure
GET /api/scenes/Level1/objects  // Returns object list
GET /api/object/Player  // Returns full object definition
```

### 2. Granular Modification Endpoints
Updates should target specific elements, not whole files:
- `PATCH /api/object/Player/behavior/Platformer`
- `POST /api/events/add`
- `DELETE /api/instance/{id}`

### 3. Validation and Safety
The API should enforce constraints that prevent invalid states:
- Schema validation
- Dependency checking
- Automatic backups before modifications
- Change preview/diff generation

### 4. Change Attribution
Track who made what changes:
- Human edits via GUI
- Agent edits via API
- Direct file edits (escape hatch)

### 5. Bidirectional Sync
Changes from either interface should be immediately visible to the other:
- GUI watches for API changes and reloads
- API monitors file changes from GUI and updates state

## Broader Applications

This pattern extends beyond game development:

**Project Management**
- Humans: Kanban boards, Gantt charts
- Agents: Task CRUD APIs, dependency graphs, status updates

**Design Systems**
- Humans: Figma, Sketch
- Agents: Design token APIs, component property updates

**Infrastructure**
- Humans: Cloud console dashboards  
- Agents: Terraform/IaC APIs, monitoring queries

**Content Management**
- Humans: WYSIWYG editors
- Agents: Structured content APIs, metadata updates

## Implementation Considerations

### Start Small
Build the API iteratively based on actual agent needs:
1. Identify high-friction agent tasks
2. Create targeted endpoints for those tasks
3. Expand coverage as patterns emerge

### MCP as the Standard
Model Context Protocol provides a standardized way to expose these APIs to agents:
- Automatic tool discovery
- Type-safe interfaces
- Cross-agent compatibility

### Local-First
Run API servers locally during development:
- No cloud dependencies
- Fast iteration
- Full control over data

### Documentation as Code
The API itself becomes executable documentation of what agents can do:
```typescript
// API schema doubles as agent capability documentation
interface GDevelopAPI {
  queryObject(name: string, scene: string): ObjectDefinition;
  updateBehavior(object: string, behavior: string, properties: object): Result;
  addEvent(scene: string, group: string, event: EventDefinition): Result;
}
```

## Measuring Success

A well-designed agent API should:
- Reduce agent context consumption by 10-100x for targeted operations
- Eliminate "hallucinated" changes that don't match data schemas
- Allow humans and agents to work concurrently without conflicts
- Make agent-assisted development feel collaborative rather than adversarial
- Enable non-technical humans to guide technical agents effectively

## Conclusion

As we build increasingly sophisticated AI-assisted workflows, **the interface becomes as important as the implementation**. By recognizing that humans and AI agents have different strengths and providing each with appropriate interfaces to the same underlying truth, we can create development workflows that are greater than the sum of their parts.

The future of agentic software development isn't about replacing human tools with agent tools—it's about building systems where both can work together seamlessly, each using the interface that makes them most effective.

---

**Next Steps:**
1. Identify high-friction points in your current agent workflows
2. Design minimal APIs to address specific pain points
3. Implement as local services or MCP servers
4. Iterate based on actual agent usage patterns
5. Generalize successful patterns across projects