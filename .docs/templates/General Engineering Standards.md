# General Engineering Standards
## Web-Based Software Development

This document outlines engineering standards and best practices for web-based software projects, regardless of framework choice (React, Vue, Angular, Next.js, Express, etc.).

---

## Table of Contents

1. [Data-Driven Design](#data-driven-design)
2. [Code Organization](#code-organization)
3. [Naming Conventions](#naming-conventions)
4. [Documentation Standards](#documentation-standards)
5. [Performance Optimization](#performance-optimization)
6. [Testing & Validation](#testing--validation)
7. [Version Control](#version-control)
8. [DevTools & Debugging](#devtools--debugging)

---

## Data-Driven Design

**Principle:** Separate application data from application logic.

-   **Application Logic:** The *how* of the application (rendering, validation, calculations)
-   **Application Data:** The *what* of the application (configuration, content, business rules)

**Benefits:**
-   Modify application behavior without changing code
-   Allow non-programmers to contribute content
-   Improve scalability and maintainability

### Example Structure

**Feature Configuration (JSON):**
```json
{
  "featureId": "user-dashboard",
  "settings": {
    "maxItems": 100,
    "refreshInterval": 5000,
    "enableNotifications": true,
    "theme": "light"
  },
  "widgets": {
    "chart": { "type": "line", "dataSource": "analytics" },
    "stats": { "type": "summary", "metrics": ["users", "revenue"] }
  }
}
```

**Form Configuration:**
```json
{
  "formId": "user_registration",
  "fields": [
    { "name": "email", "type": "email", "required": true },
    { "name": "password", "type": "password", "minLength": 8 }
  ],
  "validation": {
    "email": { "pattern": "^[^@]+@[^@]+\\.[^@]+$" }
  }
}
```

### Data File Locations

- **Application Data:** `data/` or `config/`
- **Configuration:** `config/`
- **Feature Definitions:** `data/features/`
- **Schema Definitions:** `data/schemas/`

---

## Code Organization

### Directory Structure

```
project/
├── src/                 # Source code
│   ├── pages/           # Page components/routes
│   ├── components/      # Reusable UI components
│   ├── services/        # Business logic and API calls
│   ├── hooks/           # Custom React hooks (if using React)
│   ├── utils/           # Utility functions
│   └── main.js          # Entry point
├── public/              # Static assets
│   ├── images/
│   ├── fonts/
│   └── data/           # JSON data files
├── styles/              # CSS/styling files
└── index.html           # HTML entry point
```

### Module Organization

**One Class Per File:**
- File name matches class name
- Use PascalCase for class files: `Player.js`, `TileManager.js`

**System Files:**
- Focused, single-purpose classes
- Avoid "god objects"

**Examples:**
```
✅ GOOD:
  src/components/UserProfile.js
  src/services/AuthService.js
  src/pages/Dashboard.js

❌ BAD:
  src/everything.js
  src/user_and_auth.js
```

### Code Structure

**Consistent Class Pattern:**
```javascript
/**
 * ClassName - Brief description
 */
class ClassName {
  // 1. Constructor
  constructor(params) {
    // Initialize properties
  }

  // 2. Initialization
  init() { }

  // 3. Update loop
  update(deltaTime) { }

  // 4. Public methods
  doSomething() { }

  // 5. Private helpers (prefix with _)
  _helperMethod() { }

  // 6. Cleanup
  destroy() { }
}
```

### Separation of Concerns

**Page/Route Responsibilities:**
- Page layout and composition
- Coordinate between components
- **Don't:** Put business logic in pages

**Component Responsibilities:**
- Component-specific behavior
- Local state management
- **Don't:** Access global state directly (use context/store)

**Service Responsibilities:**
- Manage shared resources
- Provide services to components
- **Don't:** Create tight coupling

---

## Naming Conventions

### JavaScript/TypeScript

**Variables and Functions:**
```javascript
// camelCase
let playerHealth = 100;
const maxSpeed = 200;

function calculateDamage(base, multiplier) {
  return base * multiplier;
}
```

**Classes/Components:**
```javascript
// PascalCase
class UserProfile { }
class DataManager { }
class Dashboard { }
```

**Constants:**
```javascript
// SCREAMING_SNAKE_CASE
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRIES = 3;
const TIMEOUT_MS = 5000;
```

**Private Members:**
```javascript
// Prefix with underscore
class Example {
  constructor() {
    this._internalState = 0;
  }

  _privateMethod() { }
}
```

**Booleans:**
```javascript
// Use descriptive prefixes
let isAuthenticated = true;
let hasPermission = false;
let canSubmit = true;
let shouldRefresh = false;
```

### Files and Directories

**JavaScript Files:**
- Components: PascalCase (`UserProfile.js`, `DataManager.js`)
- Utilities: kebab-case (`api-client.js`, `validation-helpers.js`)
- Entry points: lowercase (`main.js`, `config.js`)

**HTML/CSS Files:**
- kebab-case (`game.html`, `styles.css`, `debug-panel.html`)

**JSON Data:**
- kebab-case (`user-config.json`, `form-schema.json`)

**Directories:**
- lowercase, singular (`component/`, `service/`, `page/`)

### Asset Naming

**Use hierarchical, descriptive names:**
```javascript
// Images
'logo-primary', 'icon-user-small'
'banner-hero-desktop', 'banner-hero-mobile'

// Icons
'icon-check', 'icon-warning'
'icon-menu', 'icon-close'

// Data files
'config-production', 'config-development'
```

---

## Documentation Standards

### File Headers

```javascript
/**
 * FileName.js - Brief one-line description
 *
 * Detailed explanation:
 * - Purpose of this file
 * - Key responsibilities
 * - Important relationships
 *
 * @example
 * const manager = new AudioManager();
 * manager.playSound('jump');
 */
```

### Class Documentation

```javascript
/**
 * UserProfile - User profile management component
 *
 * Handles user data display, editing, and validation.
 * Interacts with authentication service and API layer.
 */
class UserProfile {
  /**
   * Create a new user profile component
   * @param {Object} props - Component props
   * @param {Object} user - User data object
   * @param {Object} config - Component configuration
   */
  constructor(props, user, config) {
    // ...
  }

  /**
   * Update component state
   * @param {Object} newData - Updated user data
   */
  update(newData) {
    // ...
  }
}
```

### Inline Comments

**Explain WHY, not WHAT:**
```javascript
// ✅ GOOD: Explains reasoning
// Clamp velocity to prevent tunneling through walls
if (velocity > MAX_VELOCITY) {
  velocity = MAX_VELOCITY;
}

// ❌ BAD: States the obvious
// Set velocity to max velocity
velocity = MAX_VELOCITY;
```

**Complex Algorithms:**
```javascript
/**
 * Calculate discounted price with promo code
 *
 * Formula: price * (1 - discountPercent) * quantity
 * Promo codes can provide up to 50% discount
 */
function calculateTotal(price, quantity, promoCode) {
  const discount = validatePromoCode(promoCode);
  return price * (1 - discount) * quantity;
}
```

### TODO Comments

```javascript
// TODO: Implement inventory system
// TODO Phase 2: Add multiplayer support
// FIXME: Collision detection fails at high speeds
// HACK: Temporary workaround for browser bug
// NOTE: This behavior differs from original design
```

---

## Performance Optimization

### Component Memoization

**Prevent unnecessary re-renders:**
```javascript
// ❌ BAD: Component re-renders on every parent update
function UserList({ users }) {
  return users.map(user => <UserCard key={user.id} user={user} />);
}

// ✅ GOOD: Memoize expensive components
const UserCard = React.memo(function UserCard({ user }) {
  return <div>{user.name}</div>;
});

function UserList({ users }) {
  return users.map(user => <UserCard key={user.id} user={user} />);
}
```

### Asset Optimization

**Optimize images and resources:**
- Use appropriate image formats (WebP, AVIF)
- Lazy load images below the fold
- Use CDN for static assets

### Virtual Scrolling

**Don't render off-screen items:**
```javascript
// ✅ GOOD: Only render visible items
function VirtualList({ items, itemHeight }) {
  const [scrollTop, setScrollTop] = useState(0);
  const viewportHeight = 600;

  const startIndex = Math.floor(scrollTop / itemHeight);
  const endIndex = Math.ceil((scrollTop + viewportHeight) / itemHeight);
  const visibleItems = items.slice(startIndex, endIndex);

  return visibleItems.map(item => <Item key={item.id} data={item} />);
}
```

### Efficient Data Fetching

**Avoid redundant API calls:**
- Cache responses when appropriate
- Use pagination for large datasets
- Implement debouncing for search inputs

**Batch requests:**
```javascript
// ❌ BAD: Multiple individual requests
for (let id of userIds) {
  await fetchUser(id);
}

// ✅ GOOD: Batch request
const users = await fetchUsers(userIds);
```

### Performance Monitoring

```javascript
// Track render performance
function usePerformanceMonitor() {
  useEffect(() => {
    const observer = new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        if (entry.duration > 100) {
          console.warn('Slow render:', entry.name, entry.duration);
        }
      }
    });

    observer.observe({ entryTypes: ['measure'] });
    return () => observer.disconnect();
  }, []);
}

// Use Web Vitals
import { getCLS, getFID, getFCP } from 'web-vitals';

getCLS(console.log);
getFID(console.log);
getFCP(console.log);
```

---

## Testing & Validation

### Manual Testing Checklists

**Create test lists for each feature:**
```markdown
## User Authentication Test
- [ ] Login with valid credentials (successful)
- [ ] Login with invalid credentials (shows error)
- [ ] Logout (clears session)
- [ ] Token refresh (maintains session)
- [ ] Protected routes (redirect if not authenticated)
- [ ] Password reset flow (email sent)
```

### DevTools for Testing

**Build isolated test pages:**
- Component playground
- API response viewer
- Form validator
- State debugger

**Benefits:**
- Test features in isolation
- Rapid iteration
- Visual debugging

### Console Logging

**Use consistent prefixes:**
```javascript
console.log('[Auth] User logged in:', userId);
console.warn('[API] Request failed:', endpoint);
console.error('[Validation] Invalid data:', data);

// Debug flags
const DEBUG_API = true;
if (DEBUG_API) {
  console.log('[API] Response:', response.data);
}
```

### Browser DevTools

**Use built-in browser tools:**
- Breakpoints and step debugging
- Performance profiling
- Memory profiling
- Network tab for asset loading
- Console API for debugging

---

## Version Control

### Git Workflow

**Branch Strategy:**
- `main` - Stable, production-ready
- `develop` - Active development
- Feature branches for large changes

**Commit Frequently:**
- Small, atomic commits
- One logical change per commit
- Commit working code

### Commit Messages

**Format:**
```
[Type] Brief description (50 chars)

Detailed explanation if needed (wrap at 72 chars).

- Bullet points for multiple changes
- Reference related issues/tickets
```

**Types:**
- `[Feature]` - New feature
- `[Fix]` - Bug fix
- `[Refactor]` - Code restructuring
- `[Docs]` - Documentation
- `[Test]` - Testing
- `[Perf]` - Performance
- `[Asset]` - Asset changes

**Examples:**
```
[Feature] Add player inventory system

- Created Inventory class with add/remove methods
- Added UI overlay for inventory display
- Implemented item pickup collision
```

```
[Fix] Prevent player from falling through platforms

Fixed collision detection timing issue that occurred
at high velocities.
```

### What to Commit

**Include:**
- Source code
- Documentation
- Configuration files
- Small assets (if not using LFS)

**Exclude (.gitignore):**
- `node_modules/`
- Build artifacts (`dist/`, `build/`)
- OS files (`.DS_Store`)
- IDE files (`.vscode/`, `.idea/`)
- Large binary assets (use Git LFS)
- Temporary files

---

## DevTools & Debugging

### Debug Overlays

**Add runtime debug info:**
```javascript
class DebugOverlay {
  constructor() {
    this.enabled = false;
    this.element = document.createElement('div');
    this.element.style.cssText = `
      position: fixed; top: 10px; left: 10px;
      background: rgba(0,0,0,0.8); color: #0f0;
      padding: 10px; font-family: monospace;
      z-index: 9999;
    `;
    document.body.appendChild(this.element);
  }

  update(data) {
    if (!this.enabled) return;

    this.element.textContent = [
      `Render time: ${data.renderTime}ms`,
      `API calls: ${data.apiCalls}`,
      `Active users: ${data.userCount}`,
      `Memory: ${data.memoryMB} MB`
    ].join('\n');
  }

  toggle() {
    this.enabled = !this.enabled;
    this.element.style.display = this.enabled ? 'block' : 'none';
  }
}

// Toggle with keyboard shortcut
document.addEventListener('keydown', (e) => {
  if (e.key === 'F3') {
    debugOverlay.toggle();
  }
});
```

### Console Helpers

**Global debug utilities:**
```javascript
window.DEBUG = {
  app: null, // Set to app instance

  setUser(userId) {
    this.app.auth.setUser({ id: userId });
  },

  clearCache() {
    localStorage.clear();
    sessionStorage.clear();
  },

  simulateError() {
    throw new Error('Simulated error for testing');
  },

  toggleFeatureFlag(flag) {
    this.app.features[flag] = !this.app.features[flag];
  }
};

// Usage in browser console:
// DEBUG.setUser(123)
// DEBUG.clearCache()
```

### Error Handling

**Graceful failures:**
```javascript
// ✅ GOOD: Try-catch with fallback
async function fetchUserData(userId) {
  try {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    console.error(`Failed to fetch user ${userId}:`, error);
    return getDefaultUserData(); // Fallback
  }
}

// ❌ BAD: Unhandled errors
async function fetchUserData(userId) {
  const response = await fetch(`/api/users/${userId}`);
  return await response.json(); // Will crash on error
}
```

**Validate data:**
```javascript
function validateUserData(data) {
  const required = ['id', 'email', 'name'];

  for (const field of required) {
    if (!data[field]) {
      console.error(`Missing required field: ${field}`);
      return false;
    }
  }

  return true;
}
```

---

## Additional Best Practices

### Code Clarity

**Readable over clever:**
```javascript
// ❌ BAD: Clever but obscure
const r = a && b || c ? d : e && f ? g : h;

// ✅ GOOD: Clear intent
let result;
if (a && b) {
  result = c ? d : h;
} else if (e && f) {
  result = g;
} else {
  result = h;
}
```

### Magic Numbers

**Use named constants:**
```javascript
// ❌ BAD
if (health < 20) showWarning();
velocity.y = -450;

// ✅ GOOD
const LOW_HEALTH_THRESHOLD = 20;
const JUMP_VELOCITY = -450;

if (health < LOW_HEALTH_THRESHOLD) showWarning();
velocity.y = JUMP_VELOCITY;
```

### DRY (Don't Repeat Yourself)

```javascript
// ❌ BAD: Duplicated code
if (keyLeft) {
  sprite.x -= speed;
  sprite.flipX = true;
  facing = -1;
}
if (keyRight) {
  sprite.x += speed;
  sprite.flipX = false;
  facing = 1;
}

// ✅ GOOD: Extract to function
if (keyLeft) move(-1);
if (keyRight) move(1);

function move(direction) {
  sprite.x += speed * direction;
  sprite.flipX = direction < 0;
  facing = direction;
}
```

### Early Returns

**Reduce nesting:**
```javascript
// ❌ BAD: Deep nesting
function attack(target) {
  if (canAttack) {
    if (!isReloading) {
      if (hasAmmo) {
        // Attack logic
      }
    }
  }
}

// ✅ GOOD: Guard clauses
function attack(target) {
  if (!canAttack) return;
  if (isReloading) return;
  if (!hasAmmo) return;

  // Attack logic
}
```

### Incremental Development

**Build in small steps:**
1. Write minimal code
2. Test it works
3. Commit
4. Add next small feature
5. Repeat

**Avoid:**
- Writing hundreds of lines before testing
- Implementing multiple features simultaneously
- Skipping validation steps

---

## Code Review Checklist

Before finalizing code:

- [ ] Follows naming conventions
- [ ] Data separated from logic
- [ ] Documentation added/updated
- [ ] No debug logs in production
- [ ] Performance considered
- [ ] Manual testing completed
- [ ] No console errors
- [ ] Git commit message clear

---

## Resources

### General Web Development
- Web.dev: https://web.dev/
- Frontend Masters: https://frontendmasters.com/
- CSS-Tricks: https://css-tricks.com/

### JavaScript Resources
- MDN Web Docs: https://developer.mozilla.org/
- JavaScript.info: https://javascript.info/
- You Don't Know JS: https://github.com/getify/You-Dont-Know-JS

### Performance
- Web.dev Performance: https://web.dev/performance/
- Chrome DevTools: https://developer.chrome.com/docs/devtools/

---

**Last Updated:** 2025-10-31
**Status:** Living document - update as needed
**Applicable To:** Web-based software projects (all frameworks)
