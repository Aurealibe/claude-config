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

## Critical Instruction

**YOU MUST use the Task tool with subagent_type to launch agents. NEVER read or analyze code files directly yourself.**
Agents do the heavy lifting - you orchestrate and aggregate their results.

## Tool Strategy

Use Task tool with these subagent_type values:
- `explore-docs` - Documentation lookup
- `explore-codebase` - Code pattern search
- `explore-db` - Database schema exploration
- `backend-code-optimizer` - Go code quality analysis (returns score + issues)
- `frontend-code-reviewer` - React/TS code quality analysis (returns score + issues)

---

## 1. DETERMINE SOURCE & SCOPE

### Spec Handling

- **Spec alone** (`/review spec:file.md`): Extract file list FROM the spec (exclusive source)
- **Spec + other source** (`/review spec:file.md mode:local`): Spec provides CONTEXT, other source determines files

### File Source Priority (use first available):

1. `$ARGUMENTS.commit` → `git show --name-only $ARGUMENTS.commit`
2. `$ARGUMENTS.mode` → Git diff (pushed or local)
3. `$ARGUMENTS.spec` alone → Extract files from spec
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

- Read the spec file for context (requirements, architecture, expected behavior)
- **Spec alone**: Extract all referenced files from spec as review targets
- **Spec + other source**: Use spec as context only, files come from other source

Store spec context for use in:
- Section 2.6 DEEP ANALYSIS (pass to agents)
- Section 3 ANALYZE (validate against requirements)
- Section 5 PROPOSE FIXES (align with spec intent)

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
**Spec Context**: [spec file path if provided, otherwise "None"]
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

**MANDATORY: Use Task tool with subagent_type for each agent. Do NOT read files directly.**

Launch agents in a single message (parallel execution):

| Condition | Task tool call |
|-----------|----------------|
| Backend present | `Task(subagent_type="explore-codebase", prompt="Review Go files [list]. Check duplication, unused code, Clean Architecture violations")` |
| Backend present | `Task(subagent_type="explore-codebase", prompt="Find similar existing patterns in backend/. Compare with changes")` |
| Backend present | `Task(subagent_type="explore-db", prompt="dev - Find tables related to [feature]")` |
| Frontend present | `Task(subagent_type="explore-codebase", prompt="Review React/TypeScript files [list]. Check duplication, unused hooks, pattern violations")` |
| Frontend present | `Task(subagent_type="explore-codebase", prompt="Find similar existing components/hooks. Compare patterns")` |
| External library | `Task(subagent_type="explore-docs", prompt="[library] best practices")` |

---

## 2.5 POST-EXPLORATION CHECK

After agents return, verify:
1. Context complete? If not → additional explore-codebase
2. Library docs needed? → explore-docs
3. Database schema needed? → explore-db

---

## 2.6 DEEP ANALYSIS (PARALLEL)

**MANDATORY: Use Task tool with subagent_type. Do NOT analyze files directly.**

Launch specialized agents based on detected scope. Skip if no files for that domain.

### Scope Detection Logic

1. If `$ARGUMENTS.scope` is set → use only that scope
2. Else auto-detect from file patterns:
   - `backend/**/*.go` files present → include backend
   - `frontend/src/**/*.{ts,tsx}` files present → include frontend

### Agent Dispatch (single message, parallel if both scopes)

**Include spec context in prompt if available.**

| Condition | Task tool call |
|-----------|----------------|
| Backend scope active AND backend files present | `Task(subagent_type="backend-code-optimizer", prompt="Analyze these Go files for quality: [file list]. Context from exploration: [patterns found]. Spec context: [spec summary if available]. Provide quality score and categorized issues.")` |
| Frontend scope active AND frontend files present | `Task(subagent_type="frontend-code-reviewer", prompt="Review these React/TypeScript files: [file list]. Context from exploration: [patterns found]. Spec context: [spec summary if available]. Provide quality score and categorized issues.")` |

### Skip Conditions

- If `scope=frontend` → skip `backend-code-optimizer` entirely
- If `scope=backend` → skip `frontend-code-reviewer` entirely
- If no frontend files changed → skip `frontend-code-reviewer`
- If no backend files changed → skip `backend-code-optimizer`
- If no files changed at all → report "No files to review" and stop

**Agent outputs to integrate:**
- Quality scores → merge into section 4
- Issues categorized as Critical/Important/Nice-to-have → merge into section 5
- Optimization recommendations

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

Aggregate scores from specialized agents (section 2.6). Skip domains with no agent output.

### Backend Score (from `backend-code-optimizer`)

```markdown
## Backend Quality Score: X/100

| Criterion | Score | Notes |
|-----------|-------|-------|
| Code cleanliness | X/25 | [notes] |
| Clean Architecture | X/25 | [notes] |
| Error handling | X/25 | [notes] |
| Performance | X/25 | [notes] |
```

### Frontend Score (from `frontend-code-reviewer`)

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

Weighted average based on file count per domain.

```markdown
## Global Quality Score: X/100

Formula: (Backend Score × backend_files + Frontend Score × frontend_files) / total_files

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

**This is the core purpose of the review command: identify issues AND fix them.**

After user validation, implement fixes directly using Edit tool. Do NOT just report - actually modify the files.

**Order:**
1. Critical issues first
2. Important issues
3. Nice-to-have (if approved)

**Implementation approach:**
- Use Edit tool to apply each fix
- Include simplification recommendations from agents
- Apply one fix at a time, verify it doesn't break anything
- Track progress with TodoWrite

**Rules:**
- Do not break existing functionality
- Do not change business logic unless it's a bug
- Keep changes minimal but complete
- Apply ALL approved fixes, don't stop halfway
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

- **USE AGENTS** - Task tool with subagent_type, never analyze directly
- **EXPLORE FIRST** - parallel agents before analysis
- **MEASURE** - provide quality scores per domain
- **PRIORITIZE** - Critical > Important > Nice-to-have
- **SIMPLIFY** - include simplification recommendations from agents
- **FIX WHAT YOU FIND** - after user approval, actually implement fixes with Edit tool
- **MINIMAL CHANGES** - fix issues, don't refactor unless asked
- **CHECK i18n** - verify next-intl for all frontend text
- **VERIFY BUILDS** - run build commands before completing
