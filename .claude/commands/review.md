---
name: review
description: Universal code review - auto-detects scope from git changes, spec, commit, or brief
arguments:
  - name: mode
    description: "Git mode: 'pushed' (last pushed commit) or 'local' (uncommitted changes). Default if no args."
    required: false
  - name: spec
    description: "Path to spec/plan .md file describing the feature"
    required: false
  - name: commit
    description: "Commit hash or range (e.g., 'abc123', 'HEAD', 'abc123..def456')"
    required: false
  - name: brief
    description: "Brief description of what to review"
    required: false
  - name: scope
    description: "Force scope: 'frontend', 'backend', or 'all'. Auto-detected if omitted."
    required: false
---

Think carefully for code review. Follow CLAUDE.md rules.

## Tool Strategy

Prefer specialized agents to preserve context:
- Documentation: `explore-docs` agent
- Codebase: `explore-codebase` agent
- Database: `explore-db` agent

---

## 1. DETERMINE SOURCE & SCOPE

**Input Priority** (use first available):
1. `$ARGUMENTS.spec` → Read spec, extract file list
2. `$ARGUMENTS.commit` → `git show --name-only $ARGUMENTS.commit`
3. `$ARGUMENTS.mode` → Git diff (pushed or local)
4. **Default**: `mode=local` (uncommitted changes)

### If mode = "pushed" (or default when no args and clean working tree)

```bash
git log origin/$(git rev-parse --abbrev-ref HEAD) -1 --format="%H %s"
git show --name-only --format="" HEAD
git show HEAD
```

### If mode = "local" (default)

```bash
git diff origin/$(git rev-parse --abbrev-ref HEAD)...HEAD --name-only
git status --porcelain
git diff HEAD
```

### If spec provided

- Read the spec file
- Extract all referenced files (backend and frontend)

### If commit provided

```bash
git show --name-only $ARGUMENTS.commit
git show $ARGUMENTS.commit
```

### If brief provided

- Use brief as search context
- Find related files via explore-codebase

### Auto-Categorize Files

| Pattern | Domain |
|---------|--------|
| `backend/**/*.go` | Backend |
| `frontend/src/**/*.{ts,tsx}` | Frontend |
| Other | Config/Docs |

**Apply forced scope if provided:** `$ARGUMENTS.scope`

### Report Scope

```markdown
## Review Scope

**Source**: [mode/spec/commit/brief]
**Commit**: [hash if applicable]
**Detected Scope**: [backend/frontend/both]

### Backend ([count] files)
- [file list]

### Frontend ([count] files)
- [file list]

### Other ([count] files)
- [file list]
```

---

## 2. EXPLORE (PARALLEL)

Launch agents based on detected scope (single message, parallel execution):

| Condition | Agent | Prompt |
|-----------|-------|--------|
| Backend present | explore-codebase | "Review Go files [list]. Check duplication, unused code, Clean Architecture violations" |
| Backend present | explore-codebase | "Find similar existing patterns in backend/. Compare with changes" |
| Backend present | explore-db | "dev - Find tables related to [feature]" |
| Frontend present | explore-codebase | "Review React/TypeScript files [list]. Check duplication, unused hooks, pattern violations" |
| Frontend present | explore-codebase | "Find similar existing components/hooks. Compare patterns" |
| External library | explore-docs | "[library] best practices" |

---

## 2.5 POST-EXPLORATION CHECK

After agents return, verify:
1. Context complete? If not → additional explore-codebase
2. Library docs needed? → explore-docs
3. Database schema needed? → explore-db

---

## 3. ANALYZE

Read each changed file and produce analysis.

```markdown
## Code Review Analysis

### Files Reviewed
- `path/to/file` - [purpose of changes]
```

### Backend Analysis (if backend files present)

```markdown
### Backend Issues

#### Duplications Found
- [description] in files X and Y

#### Clean Architecture Violations
- [description] - should follow pattern from [existing file]

#### Unused Code
- `function_name` in `file.go` - never called

#### Context Propagation Issues
- Missing context.Context in [function]

#### Error Handling Issues
- [description]

#### Performance Issues
- [description]
```

### Frontend Analysis (if frontend files present)

```markdown
### Frontend Issues

#### Duplications Found
- [description] in components X and Y

#### Pattern Violations
- [description] - should follow pattern from [existing component]

#### Unused Code
- `ComponentName` in `file.tsx` - never used

#### TypeScript Issues
- `any` type used in [file]
- Missing types for [props/state]

#### i18n Issues
- Hardcoded string "[text]" in [component] - use next-intl

#### Accessibility Issues
- Missing aria-label in [component]
- Missing keyboard navigation in [component]

#### Performance Issues
- Missing memoization in [component]
- Unnecessary re-renders in [component]
- Missing Suspense boundaries
```

### Positive Observations

```markdown
### Positive Observations
- [what was done well]
```

---

## 4. QUALITY SCORES

Provide separate scores per domain.

### Backend Score (if applicable)

```markdown
## Backend Quality Score: X/100

| Criterion | Score | Notes |
|-----------|-------|-------|
| Code cleanliness | X/25 | [notes] |
| Clean Architecture | X/25 | [notes] |
| Error handling | X/25 | [notes] |
| Performance | X/25 | [notes] |
```

### Frontend Score (if applicable)

```markdown
## Frontend Quality Score: X/100

| Criterion | Score | Notes |
|-----------|-------|-------|
| Code correctness | X/20 | [notes] |
| TypeScript usage | X/20 | [notes] |
| Performance | X/20 | [notes] |
| i18n compliance | X/20 | [notes] |
| Best practices | X/20 | [notes] |
```

### Global Score

```markdown
## Global Quality Score: X/100

[1-2 sentence overall assessment]
```

---

## 5. PROPOSE FIXES

```markdown
## Recommended Fixes

### Critical (must fix)
1. **[Issue]**
   - File: `path:line`
   - Problem: [description]
   - Fix: [how to fix]

### Important (should fix)
1. ...

### Nice-to-have
1. ...
```

---

## 6. VALIDATE

Ask with AskUserQuestion: "Code review complete. What would you like to do?"

Options:
- "Apply all fixes"
- "Apply Critical only"
- "Apply Critical + Important"
- "No fixes needed"

---

## 7. IMPLEMENT (if requested)

After validation, implement directly.

**Order:**
1. Critical issues first
2. Important issues
3. Nice-to-have (if approved)

**Rules:**
- Do not break existing functionality
- Do not change business logic unless it's a bug
- Keep changes minimal
- If logic seems wrong, ASK before changing

---

## 8. VERIFY

Run verification based on scope:

```bash
# Backend (if backend files changed)
cd backend && go build ./... && go vet ./...

# Frontend (if frontend files changed)
cd frontend && npm run build
```

Database (if schema changes): `mcp__supabase-dev__get_advisors`

---

## 9. SUMMARY

```markdown
## Review Complete

### Source: [mode/spec/commit/brief]
### Scope: [backend/frontend/both]

### Files Reviewed
- [count] backend files
- [count] frontend files

### Issues Found
- [count] Critical
- [count] Important
- [count] Minor

### Fixes Applied
- [list or "None requested"]

### Final Scores
- Backend: X/100
- Frontend: X/100
- Global: X/100

### Recommendations
- [follow-up actions]
```

---

## Rules

- **EXPLORE FIRST** - parallel agents before analysis
- **READ BEFORE JUDGING** - read actual file content
- **MEASURE** - provide quality scores per domain
- **PRIORITIZE** - Critical > Important > Nice-to-have
- **MINIMAL CHANGES** - fix issues, don't refactor unless asked
- **CHECK i18n** - verify next-intl for all frontend text
- **VERIFY BUILDS** - run build commands before completing
