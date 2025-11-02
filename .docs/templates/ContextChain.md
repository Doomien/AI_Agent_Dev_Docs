# Context Chain
## Quick Context for Active Development

**⚠️ KEEP THIS DOCUMENT LEAN ⚠️**

This document provides rapid orientation for agents starting new sessions. It should be updated at phase transitions or when prompted, but must remain concise. Verbose details belong in ProjectPlan, Devlog, or technical documentation.

---

## How to Use This Document

### When to Update
- ✅ Major phase transitions (new system, new feature area)
- ✅ Significant architectural decisions made
- ✅ New blockers identified
- ✅ When explicitly prompted
- ❌ NOT after every small task
- ❌ NOT for routine progress updates (use Devlog.md)

### How to Keep It Lean

**Write in fragments, not prose:**
```
✅ GOOD: "Authentication system: 3/5 tasks done, UI pending, API integration complete"
❌ BAD: "We have been working on the authentication system and have made significant
         progress. So far we have completed three out of five planned tasks..."
```

**Use bullet points, not paragraphs:**
```
✅ GOOD:
- User authentication: Complete
- Dashboard API: In progress (endpoints done, validation next)
- Data migration: Blocked (needs schema decision)

❌ BAD:
The user authentication system has been completed and is working well. We are
currently in the middle of implementing the dashboard API, where we've finished the
endpoints component...
```

**Link to details, don't duplicate them:**
```
✅ GOOD: "Auth architecture decided (see ProjectPlan.md § Authentication System)"
❌ BAD: [Reproducing the entire architecture here]
```

**Maximum Length:** Target 50 lines or less. If it grows beyond 100 lines, archive old context to `ContextChain_Archive.md` and start fresh.

---

## Current Phase

**Phase:** [Current development phase]  
**Sprint/Focus:** [What's the immediate goal]  
**Target Completion:** [Rough timeframe if known]

---

## Recent Progress (Last 7 Days)

Keep this to 3-7 bullet points maximum:

- [Major accomplishment 1]
- [Major accomplishment 2]
- [System/feature completed]

---

## Active Work

### Current System/Feature
**Focus:** [What's being built right now]  
**Status:** [% complete or stage description]  
**Owner:** [PM_Agent | IC_Agent | Paused]

### Next Up
1. [Immediate next task/system]
2. [Following priority]

---

## Critical Decisions Made

Only include decisions that affect future work:

**[Date] - [Decision Topic]**
- **Decided:** [What was chosen]
- **Why:** [One sentence rationale]
- **Impact:** [What this affects]

**[Date] - [Decision Topic]**
- **Decided:** [What was chosen]
- **Why:** [One sentence rationale]
- **Impact:** [What this affects]

---

## Active Blockers

Current obstacles preventing progress:

**[Blocker Name]**
- **Blocks:** [What can't proceed]
- **Needs:** [What would unblock it]
- **Status:** [Awaiting decision | Being investigated | etc.]

**[Blocker Name]**
- **Blocks:** [What can't proceed]
- **Needs:** [What would unblock it]
- **Status:** [Awaiting decision | Being investigated | etc.]

---

## Known Issues

Problems that exist but aren't blocking:

- [Issue 1]: [Brief description] → [Tracked in: Devlog/GitHub/etc.]
- [Issue 2]: [Brief description] → [Tracked in: Devlog/GitHub/etc.]

---

## Quick Links

Essential documents for current work:

- **ProjectPlan.md** → [Specific section if relevant]
- **Devlog.md** → [Recent entry date]
- **Handoff.md** → [Last session date]
- **[System Documentation]** → [If applicable]

---

## Context for New Sessions

Brief orientation for agents starting fresh:

**What's working:**
- [Completed system 1]
- [Completed system 2]

**What's in progress:**
- [Active work item]

**What's next:**
- [Immediate priority]

**Important notes:**
- [Anything unusual about current state]
- [Temporary workarounds in place]

---

## Template for Updates

Copy this when adding new context:

```markdown
## Update: [Date]

**Phase:** [If changed]
**Progress:** [What was accomplished]
**Decisions:** [Any decisions made]
**Blockers:** [New or resolved]
**Next:** [What's coming next]
```

---

## Archive Trigger

**Archive when:**
- Document exceeds 100 lines
- Phase completes and new phase begins
- Major pivot in project direction

**How to archive:**
1. Create/append to `ContextChain_Archive.md`
2. Add header: `## Archived: [Date Range]`
3. Copy current content
4. Clear this document and start fresh
5. Keep only "Critical Decisions Made" that still matter

---

## Example (Good)

This is what a well-maintained ContextChain looks like:

```markdown
## Current Phase
**Phase:** Core Features
**Sprint:** User Authentication & Dashboard
**Target:** End of week

## Recent Progress
- User authentication complete (login, signup, logout)
- JWT token management working
- Protected routes functional

## Active Work
**Focus:** Dashboard API integration
**Status:** 60% (endpoints done, UI next)
**Owner:** IC_Agent

### Next Up
1. Dashboard UI components
2. Data visualization

## Critical Decisions Made
**2025-10-29 - State Management**
- **Decided:** Use React Context + useReducer
- **Why:** Simpler than Redux for this app size
- **Impact:** All components use shared context

## Active Blockers
None currently

## Context for New Sessions
**What's working:** Auth flows complete, API endpoints ready
**What's in progress:** Dashboard UI (70% done)
**What's next:** Data visualization components
**Important:** API schemas in data/schemas/, NOT hardcoded
```

---

## Example (Too Verbose - DON'T DO THIS)

```markdown
## Recent Progress

We've made really excellent progress on the user authentication system over
the last few days. The system now includes comprehensive authentication controls
including login and signup flows, JWT token generation with proper
expiration handling, and smooth redirect logic when the user completes
the authentication process. Additionally, we implemented a protected route
system that works quite well...

[This continues for 200+ lines]
```

**Problem:** Way too detailed, redundant with Devlog, hard to scan quickly.

---

**Last Updated:** [Date]  
**Status:** Active development tracking  
**Target Length:** < 100 lines
