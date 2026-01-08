---
name: dev
description: Develop phase with exploration and plan validation
arguments:
  - name: spec
    description: "Path to spec .md file"
    required: true
  - name: phase
    description: "Phase number (e.g., '2' or '2-3' for multiple)"
    required: true
  - name: done
    description: "Phases completed (e.g., '0-1')"
    required: false
---

Follow CLAUDE.md rules.

## Ultra Think Strategy

Ultra think before each phase transition:
- After exploration results: reflect on completeness before planning
- Before implementation: consider edge cases, patterns to follow, potential issues
- After validation: ensure the approach aligns with user intent

---

## 1. UNDERSTAND

- Read spec: `$ARGUMENTS.spec`
- Identify phase: `$ARGUMENTS.phase`
- Phases completed: `$ARGUMENTS.done`
- Extract from phase description:
  - **Scope**: backend / frontend / both
  - **Files** to create/modify
  - **Libraries** needed

---

## 2. EXPLORE (PARALLEL)

Launch agents based on detected scope (single message, parallel execution):

| Scope | Agents |
|-------|--------|
| backend | explore-codebase (backend/), explore-db (dev) |
| frontend | explore-codebase (frontend/src/) |
| both | All above |

Add explore-docs for any external libraries mentioned.

---

## 2.5 POST-EXPLORATION CHECK

After agents return, verify completeness:
1. Database info needed but missing? → launch explore-db
2. Library docs needed but missing? → launch explore-docs
3. Similar patterns not found? → additional explore-codebase

---

## 3. SHOW PLAN

Display enriched plan:

```markdown
## Phase $ARGUMENTS.phase

### Files to Create
- `path/file` - [purpose]

### Files to Modify
- `path/file:XX` - [what to change]

### Patterns to Reuse (from exploration)
- [existing code patterns found]

### Order
Backend: Domain → Application → Infrastructure → Presentation
Frontend: Types → API → Hooks → Components → Pages
```

---

## 4. VALIDATE

Ask with AskUserQuestion: "Proceed with implementation?"
- "Implement"
- "Modify"

---

## 5. IMPLEMENT

After validation, implement in appropriate order:

**Backend:** Domain → Application → Infrastructure → Presentation
- context.Context as first parameter for I/O
- Follow Clean Architecture patterns
- Complete error handling

**Frontend:** Types → API → Hooks → Components → Pages
- next-intl for ALL user-facing text
- Shadcn UI for standard components
- Strict TypeScript (no `any`)

For significant UI: `Skill(skill="frontend-design:frontend-design")`

---

## 6. VERIFY

```bash
# Backend
cd backend && go build ./... && go vet ./...
# Frontend
cd frontend && npm run build
```

Database verification: `mcp__supabase-dev__XXXX`

---

## 7. UPDATE SPEC

Check off completed items in the Execution Plan of the spec file.

---

## Rules

- **EXPLORE FIRST** - Always explore before implementing
- **REUSE** - Existing code/patterns as much as possible
- **VALIDATE** - Always ask before implementing
- **STAY IN SCOPE** - Only the specified phase
