---
name: oneshot
description: Quick implementation without a full spec file
arguments:
  - name: task
    description: "What to implement"
    required: true
---

Follow CLAUDE.md rules.

## Ultra Think Strategy

Ultra think before each phase transition:
- After clarification: ensure you understand exactly what to build
- After exploration results: reflect on completeness before planning
- Before implementation: consider edge cases, patterns to follow, potential issues
- After validation: ensure the approach aligns with user intent

---

## 1. CLARIFY REQUIREMENTS

Use AskUserQuestion to clarify before exploration:

### Must clarify (if not specified)
- Error messages for user-facing failures?
- Default values for new fields?
- Validation rules?
- What triggers state changes?

### UX Decisions (if not specified)
- What happens on success? (toast, redirect, refresh?)
- What happens on error?
- Confirmation dialogs needed?

### Permissions (if not specified)
- Who can perform each action?
- RLS policy rules?

### Scope (if not specified)
- Backend only, frontend only, or both?
- Database changes needed?

Do not proceed until critical details are clarified.

---

## 2. EXPLORE (PARALLEL)

Launch agents based on task scope (single message, parallel execution):

| Need | Agent | Prompt |
|------|-------|--------|
| Backend | explore-codebase | "Find all [task] related code in backend/" |
| Frontend | explore-codebase | "Find all [task] related code in frontend/src/" |
| Database | explore-db | "dev - relevant tables for [task]" |
| Library | explore-docs | "[library] documentation" |
| External | websearch | "[topic] best practices 2025" |

---

## 2.5 POST-EXPLORATION CHECK

After agents return, verify:
1. Database info complete? If not -> launch explore-db
2. Library docs complete? If not -> launch explore-docs
3. For modifications: Do I have ALL files where similar patterns exist?

---

## 3. PLAN

Write detailed implementation plan:

```markdown
## Implementation Plan

### Database changes (if applicable)
- Migration: [description]

### Backend changes
- Files to create/modify with line numbers

### Frontend changes
- Files to create/modify with line numbers

### Patterns to follow (from exploration)
- [code snippets to replicate]

### Order
1. [step 1]
2. [step 2]
```

---

## 4. VALIDATE

Ask with AskUserQuestion: "Approve this plan?"
- "Implement"
- "Modify"

---

## 5. IMPLEMENT

After validation, implement:
- Backend first (Domain -> Application -> Infrastructure -> Presentation)
- Frontend second (Types -> API -> Hooks -> Components -> Pages)

For significant UI: `Skill(skill="frontend-design:frontend-design")`

**Rules:**
- Follow patterns from exploration
- No hardcoded values
- Complete error handling

---

## 6. VERIFY

```bash
cd backend && go build ./... && go vet ./...
cd frontend && npm run build
```

---

## Rules

- **CLARIFY FIRST** - understand requirements before exploring
- **EXPLORE SECOND** - parallel exploration before planning
- **VALIDATE** - always ask before implementing
- **STAY IN SCOPE** - only what's needed
