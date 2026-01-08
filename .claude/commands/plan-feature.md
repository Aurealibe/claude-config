---
name: plan-feature
description: Create complete development plan with parallel exploration
arguments:
  - name: name
    description: "Short name for the feature (e.g., 'prospect-kanban')"
    required: true
---

Follow CLAUDE.md rules.

**Output file:** `{$ARGUMENTS.name}_FEATURE.md` (uppercase, root directory)

## Ultra Think Strategy

Ultra think before each phase transition:
- After exploration results: reflect on completeness before planning
- Before writing spec: consider architecture, edge cases, future maintainability
- After validation: ensure the plan is comprehensive and actionable

---

## 1. GATHER REQUIREMENTS

Ask user for:
- Detailed feature description
- Mockups/screenshots if available
- Business rules and edge cases
- Integrations with existing features

Do not proceed until requirements are clear.

---

## 1.5 CLARIFY DETAILS

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

Do not proceed until critical details are clarified.

---

## 2. EXPLORE (PARALLEL)

Launch agents based on feature scope (single message, parallel execution):

| Need | Agent | Prompt |
|------|-------|--------|
| Backend | explore-codebase | "Find all [feature] related code in backend/ - entities, usecases, repos, handlers" |
| Frontend | explore-codebase | "Find all [feature] related code in frontend/src/ - components, hooks, types, pages" |
| Database | explore-db | "dev - Find tables related to [feature], check schema and relationships" |
| Library | explore-docs | "[library] [specific feature] documentation" |
| External | websearch | "[topic] best practices 2025" |

---

## 2.5 POST-EXPLORATION CHECK

After agents return, verify:
1. Database info complete? If not -> launch explore-db
2. Library docs complete? If not -> launch explore-docs

---

## 3. VALIDATE ARCHITECTURE

Display architecture plan:

```markdown
## Architecture Plan - [Feature Name]

### Database
- Tables to create: [list with columns]
- Tables to modify: [changes]
- RLS policies needed

### Backend (Go Clean Architecture)
- Entities: [list]
- Usecases: [list with descriptions]
- Handlers: [endpoints]
- Code to reuse: [from exploration]

### Frontend (Next.js)
- Types, Components, Hooks, Pages
- Code to reuse: [from exploration]

### Libraries / Best Practices
- [from exploration]
```

Ask with AskUserQuestion: "Validate this architecture?"
- "Validate"
- "Modify"

---

## 4. WRITE SPEC FILE

After validation, write complete spec to `{$ARGUMENTS.name}_FEATURE.md`:

Structure:
1. Overview (objective, summary, tech stack)
2. Context and Motivation
3. Functional Specifications (detailed behavior, rules, edge cases)
4. Technical Architecture (existing files to modify, new files to create)
5. Database (migrations, columns, RLS policies)
6. Backend Implementation (phases: Domain -> Infrastructure -> Application -> Presentation)
7. Frontend Implementation (phases: Types/API -> Hooks -> Components -> Pages)
8. Execution Plan (checkboxes for each task)
9. Important Notes (compatibility, performance, security)

### Backend Phase Rules
- Order: Domain -> Infrastructure -> Application -> Presentation
- Max 5 items per phase - split if more
- Separate CRUD usecases from business logic usecases
- Each phase must compile independently

---

## 5. DELIVER

- Confirm file created
- Summarize the phases
- Indicate next steps:
  - `/dev-backend spec=[name]_FEATURE.md phase=1`
  - `/dev-frontend spec=[name]_FEATURE.md phase=8`

---

## Rules

- **EXPLORE FIRST** - parallel exploration before the plan
- **CONTEXT IS KEY** - spec must be detailed enough for a new session
- **VALIDATE** - user validation before writing spec
- **CHECKBOXES** - execution plan with checkboxes to track progress
