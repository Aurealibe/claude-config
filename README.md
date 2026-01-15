# Claude Code Config

**A comprehensive configuration framework for Claude Code** - Transform Claude into an autonomous development assistant with specialized agents, workflow commands, and audio notifications.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Author](https://img.shields.io/badge/Author-@Aurealibe-blue.svg)](https://github.com/Aurealibe)

---

## Introduction

### What is Claude Code?

[Claude Code](https://code.claude.com/docs/en/overview) is Anthropic's official CLI tool that allows you to use Claude directly in your terminal. It can read and modify files, execute commands, navigate your codebase, and much more.

### Why this repository?

This repository provides an **advanced and extensible configuration** for Claude Code, including:

- **6 Specialized agents** for parallel exploration and code review (code, database, documentation, web, backend optimization, frontend review)
- **7 Workflow commands** to automate the complete development cycle
- **Audio notification system** to know when Claude has finished or is waiting for your input
- **Customizable development guidelines** for your tech stack
- **Architecture patterns** transferable to any language/framework

### What it actually brings

| Without this config | With this config |
|---------------------|------------------|
| Claude explores sequentially | Parallel exploration with 4 specialized agents |
| No structured workflow | Workflow: Explore → Plan → Validate → Implement → Verify |
| You have to watch Claude | Audio notifications when Claude finishes or waits |
| Ad-hoc approach | Standardized commands (`/commits`, `/review`, `/hotfix`, etc.) |
| Claude may invent patterns | Guidelines force reuse of existing code |

---

## Best Practices - My Methodology

Here's how I use this configuration in production to consistently get **production-ready code**.

### 1. Launch Claude Code

```bash
# Normal mode (recommended)
claude

# Then enable "thinking" mode (ultrathink) for better results
```

**Advanced option (more dangerous):**
```bash
claude --dangerously-skip-permissions
```
This option disables security confirmations. Claude can execute commands without asking. Only use if you know what you're doing.

Thinking mode allows Claude to think more deeply before acting.

### 2. Recommended Workflow

```
┌─────────────────────────────────────────────────────────────┐
│  /plan-feature name="my-feature"                            │
│  → Brainstorming + spec file writing                        │
│  → Generates: MY_FEATURE_FEATURE.md                         │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  /dev spec="MY_FEATURE_FEATURE.md" phase="1"                │
│  → Develops phase 1 (e.g., Domain layer)                    │
│  → /clear after completion                                  │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  /dev spec="MY_FEATURE_FEATURE.md" phase="2" done="1"       │
│  → Develops phase 2 (e.g., Infrastructure)                  │
│  → /clear after completion                                  │
└─────────────────────────────────────────────────────────────┘
                            ↓
                          ...
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  /review spec="MY_FEATURE_FEATURE.md"                       │
│  → Code review against spec                                 │
│  → Corrections if needed                                    │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  /commits                                                   │
│  → Automatic commit (Conventional Commits)                  │
│  → Push to repo                                             │
└─────────────────────────────────────────────────────────────┘
```

### 3. Golden Rule: Empty context = better results

**CRITICAL**: Never exceed Claude's context.

- **One dev phase = one new chat**
- After each session, do `/clear` and start fresh
- Always prefer an empty context

Why? Claude performs better with a fresh context. An overloaded context degrades response quality.

### 4. Why this methodology > default plan mode

You can use Claude Code with default plan mode, but my methodology is better because:

| Default plan mode | This methodology |
|-------------------|------------------|
| Internal agents not configurable | **Custom configurable agents** (add your own) |
| Sequential exploration | **Parallel exploration** (docs + code + db simultaneously) |
| Plans once at the beginning | Planning at EVERY command |
| No intermediate validation | Mandatory user validation before each implementation |
| Accumulating context | Fresh context at each phase |
| Variable results | Consistent results |

**The real value: parallel agents.**

When you launch a command (`/dev`, `/hotfix`, `/explore`...), it automatically launches multiple agents in parallel:

```
┌─────────────────────────────────────────────────────────────┐
│                    YOUR COMMAND                              │
│                                                              │
│   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│   │ explore-    │  │ explore-db  │  │ explore-    │         │
│   │ codebase    │  │             │  │ docs        │         │
│   │             │  │  (schema,   │  │             │         │
│   │ (patterns,  │  │   tables,   │  │ (libraries, │         │
│   │  existing   │  │   RLS)      │  │  APIs)      │         │
│   │  code)      │  │             │  │             │         │
│   └──────┬──────┘  └──────┬──────┘  └──────┬──────┘         │
│          │                │                │                 │
│          └────────────────┼────────────────┘                 │
│                           ▼                                  │
│              Complete context in seconds                     │
└─────────────────────────────────────────────────────────────┘
```

- **Faster**: 3 agents in parallel vs sequential exploration
- **Better results**: complete context before any action
- **Configurable**: add your own agents in `.claude/agents/`

Claude's default plan mode uses its own internal agents, slower and not modifiable. With this config, you control exactly what Claude explores.

### 5. Summary

```bash
# 1. Plan the feature
/plan-feature name="user-auth"

# 2. Dev phase by phase (new chat each time)
/dev spec="USER_AUTH_FEATURE.md" phase="1"
# → /clear

/dev spec="USER_AUTH_FEATURE.md" phase="2" done="1"
# → /clear

# 3. Final review
/review spec="USER_AUTH_FEATURE.md"

# 4. Commit
/commits
```

The goal is to easily achieve **production-grade code**. The configuration is minimal, but results are consistent.

---

## Table of Contents

- [Quick Start](#quick-start)
- [Repository Structure](#repository-structure)
- [CLAUDE.md - The Guidelines File](#claudemd---the-guidelines-file)
- [Agents](#agents)
  - [explore-codebase](#explore-codebase)
  - [explore-db](#explore-db)
  - [explore-docs](#explore-docs)
  - [websearch](#websearch)
  - [backend-code-optimizer](#backend-code-optimizer)
  - [frontend-code-reviewer](#frontend-code-reviewer)
- [Best Practices - My Methodology](#best-practices---my-methodology)
- [Commands](#commands)
  - [commits](#commits)
  - [dev](#dev)
  - [explore](#explore)
  - [hotfix](#hotfix)
  - [oneshot](#oneshot)
  - [plan-feature](#plan-feature)
  - [review](#review)
- [Settings & Hooks](#settings--hooks)
- [Audio System](#audio-system)
- [Customization for Your Stack](#customization-for-your-stack)
- [Philosophy & Design Patterns](#philosophy--design-patterns)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

---

## Quick Start

### Prerequisites

- [Claude Code](https://code.claude.com/docs/en/overview) installed and configured
- An existing or new project
- (Optional) For audio notifications: macOS with `afplay`, or Linux/Windows equivalent

### Installation

1. **Clone this repository** into your project or copy the `.claude/` folder:

```bash
# Option 1: Clone the entire repo
git clone https://github.com/Aurealibe/claude-config.git
cp -r claude-config/.claude/ your-project/.claude/
cp claude-config/CLAUDE.md your-project/CLAUDE.md

# Option 2: Add as submodule (to keep updates)
cd your-project
git submodule add https://github.com/Aurealibe/claude-config.git .claude-config
ln -s .claude-config/.claude .claude
ln -s .claude-config/CLAUDE.md CLAUDE.md
```

2. **Configure audio paths** in `.claude/settings.json`:

```json
{
  "hooks": {
    "Stop": [{
      "command": "afplay /path/to/your/project/.claude/song/finish.mp3 &"
    }],
    "Notification": [{
      "matcher": "idle_prompt",
      "command": "afplay /path/to/your/project/.claude/song/need-human.mp3 &"
    }]
  }
}
```

3. **Customize `CLAUDE.md`** for your stack (see [Customization](#customization-for-your-stack))

### First Use

```bash
# Launch Claude Code in your project
cd your-project
claude

# Test a command
> /explore question="How does authentication work in this project?"

# Or launch a dev workflow
> /dev spec="specs/my-feature.md" phase="1"
```

---

## Repository Structure

```
claude-config/
├── .claude/                    # Claude Code configuration
│   ├── agents/                 # 6 specialized agents
│   │   ├── explore-codebase.md # Search in source code
│   │   ├── explore-db.md       # Supabase database exploration
│   │   ├── explore-docs.md     # Library documentation
│   │   ├── websearch.md        # Quick web search
│   │   ├── backend-code-optimizer.md # Go code optimization
│   │   └── frontend-code-reviewer.md # React/TypeScript code review
│   │
│   ├── commands/               # 7 workflow commands
│   │   ├── commits.md          # Automatic git commit (Conventional Commits)
│   │   ├── dev.md              # Phase-based development
│   │   ├── explore.md          # Deep investigation
│   │   ├── hotfix.md           # Bug fixes
│   │   ├── oneshot.md          # Quick implementation
│   │   ├── plan-feature.md     # Complete feature planning
│   │   └── review.md           # Automated code review
│   │
│   ├── song/                   # Audio files for notifications
│   │   ├── finish.mp3          # Plays when a task is completed
│   │   └── need-human.mp3      # Plays when Claude waits for your input
│   │
│   └── settings.json           # Claude Code hooks configuration
│
├── CLAUDE.md                   # Development guidelines (instructions for Claude)
├── LICENSE                     # MIT License
└── README.md                   # This file
```

### Explanation of Each Element

| File/Folder | Role |
|-------------|------|
| `.claude/agents/` | Specialized agents launched in parallel to collect context quickly |
| `.claude/commands/` | Commands invocable with `/name` that orchestrate complete workflows |
| `.claude/song/` | Notification sounds for audio feedback |
| `.claude/settings.json` | Hooks that trigger actions on certain events |
| `CLAUDE.md` | Instructions that Claude reads at startup - defines your conventions and patterns |

---

## CLAUDE.md - The Guidelines File

The `CLAUDE.md` file at the root of your project is **automatically read by Claude Code** at startup. It's your way to define:

- Your project conventions
- Architecture to follow
- Patterns to reuse
- Strict rules (security, i18n, etc.)

### Structure of the Provided CLAUDE.md

```markdown
# Main sections

1. Project Overview       → Tech stack, project IDs
2. Development Principles → Fundamental rules (DB, reuse, clean code, etc.)
3. Core Architecture      → Backend layers, frontend patterns
4. Development Process    → Pre-implementation workflow
5. Naming Conventions     → Naming standards
6. Common Commands        → Useful commands
```

### Section by Section

#### 1. Project Overview

```markdown
## Project Overview

**AUREA** Claude Code stack (Next.js 16 + Go).

- **Frontend**: Next.js 16 (App Router), TypeScript, TailwindCSS, React Query, Shadcn UI
- **Backend**: Go (Golang), Fiber, Clean Architecture
- **Database**: Supabase (PostgreSQL), Redis
- **Infrastructure**: Docker
```

**How to customize:**

Replace with your stack. For example for Vue + Python:

```markdown
## Project Overview

**MyProject** - Vue.js + Python stack.

- **Frontend**: Vue 3 (Composition API), TypeScript, TailwindCSS, Pinia
- **Backend**: Python, FastAPI, Clean Architecture
- **Database**: PostgreSQL, Redis
- **Infrastructure**: Docker
```

#### 2. Fundamental Development Principles

These principles are **universal** and applicable to any stack:

| Principle | Explanation | Why it matters |
|-----------|-------------|----------------|
| **Database via MCP** | Use MCP tools for DB queries | Traceability, security, no direct connections |
| **Maximum Reuse** | ALWAYS search for existing before creating | Avoids duplication, maintains consistency |
| **Dynamic & Modular** | No hardcoded values | Flexibility, centralized configuration |
| **Clean Code** | No dead code, DRY | Maintainability |
| **Error Handling** | Handle ALL error cases | Robustness |
| **Up-to-Date Docs** | Check docs before using a lib | Avoids deprecated APIs |
| **i18n** | No hardcoded strings | Internationalization |
| **Context Propagation** | `context.Context` in Go | Tracing, cancellation, timeouts |

#### 3. Core Architecture

The provided example uses **Clean Architecture** with 4 layers:

```
Domain → Application → Infrastructure → Presentation
```

**Adaptable to any stack:**

| Layer | Go (Fiber) | Python (FastAPI) | Node.js (NestJS) | Rust (Axum) |
|-------|------------|------------------|------------------|-------------|
| Domain | `internal/domain` | `app/domain` | `src/domain` | `src/domain` |
| Application | `internal/application` | `app/application` | `src/application` | `src/application` |
| Infrastructure | `internal/infrastructure` | `app/infrastructure` | `src/infrastructure` | `src/infrastructure` |
| Presentation | `internal/presentation` | `app/api` | `src/controllers` | `src/handlers` |

---

## Agents

Agents are **specialized sub-processes** that Claude can launch in parallel to collect context quickly. They are **read-only** (don't modify anything) and return a structured report.

### How Agents Work

```
┌─────────────────────────────────────────────────────────────┐
│                    CLAUDE (Main)                             │
│                                                              │
│  "I need to understand how auth works..."                   │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │ explore-     │  │ explore-db   │  │ explore-     │       │
│  │ codebase     │  │              │  │ docs         │       │
│  │              │  │ "Tables      │  │              │       │
│  │ "Search      │  │  users,      │  │ "JWT docs,   │       │
│  │  auth in     │  │  sessions"   │  │  Fiber       │       │
│  │  backend/"   │  │              │  │  middleware" │       │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘       │
│         │                 │                 │                │
│         ▼                 ▼                 ▼                │
│  ┌──────────────────────────────────────────────────┐       │
│  │              COMBINED RESULTS                     │       │
│  │  - 15 Go files with auth                         │       │
│  │  - Schema for users/sessions tables              │       │
│  │  - JWT Fiber v2 patterns                         │       │
│  └──────────────────────────────────────────────────┘       │
│                                                              │
│  → Claude now has full context to act                       │
└─────────────────────────────────────────────────────────────┘
```

---

### explore-codebase

**Purpose:** Find existing code, patterns, and dependencies in your project.

| Property | Value |
|----------|-------|
| **File** | `.claude/agents/explore-codebase.md` |
| **Model** | Haiku (lightweight, fast) |
| **Tools** | `Read`, `Grep`, `Glob` |
| **Mode** | Read-only |

#### Search Strategy

1. **Broad search** - Grep to find entry points
2. **Parallel searches** - Related keywords simultaneously
3. **Full read** - Complete files with context
4. **Follow imports** - Dependency discovery

#### What It Finds

- Similar existing functions/classes/components
- Established patterns in the project
- Configuration files
- Database schemas
- API endpoints
- Tests and usage examples
- Reusable utility functions

#### Output Format

```markdown
## Exploration Results

### Relevant Files
- `backend/internal/auth/jwt.go:45` - Existing JWT middleware
- `backend/internal/auth/session.go:12` - Session management

### Identified Patterns
- Repository pattern used for all DB access
- Middleware chain: Logger → Auth → RateLimit → Handler

### Reusable Code
```go
// backend/internal/auth/jwt.go:45-67
func ValidateToken(ctx context.Context, token string) (*Claims, error) {
    // ... existing implementation
}
```

### Dependencies
- `github.com/golang-jwt/jwt/v5` - JWT library
- `backend/internal/config` - Configuration
```

#### Usage Example

When the `/explore` or `/dev` command is launched, this agent is automatically invoked:

```
Claude: "I'm launching explore-codebase to search for auth patterns..."

Agent explore-codebase:
→ Grep "auth" in backend/
→ Grep "middleware" in backend/
→ Grep "jwt" in backend/
→ Full read of found files
→ Follow imports

Return: "Found 8 files, established middleware pattern, JWT already implemented..."
```

---

### explore-db

**Purpose:** Explore the Supabase database schema (PostgreSQL).

| Property | Value |
|----------|-------|
| **File** | `.claude/agents/explore-db.md` |
| **Model** | Haiku |
| **Tools** | MCP Supabase (`list_tables`, `execute_sql`, etc.) |
| **Mode** | Read-only (SELECT only) |

#### Environment Detection

The agent automatically detects the environment from your prompt:

```
"Explore the dev DB" → Uses mcp__supabase-dev__*
"Explore the prod DB" → Uses mcp__supabase-prod__*
(default) → Uses dev
```

#### Available MCP Tools

| Tool | Usage |
|------|-------|
| `list_tables` | List all tables with columns |
| `list_extensions` | Active PostgreSQL extensions |
| `list_migrations` | Migration history |
| `execute_sql` | SELECT queries (read-only) |
| `get_advisors` | Security/performance alerts |

#### Search Strategy

1. `list_tables` to understand the global schema
2. `execute_sql` for targeted queries on relevant tables
3. `get_advisors` to identify issues
4. Trace foreign keys to understand relationships

#### What It Finds

- Table and column structure
- Foreign key relationships
- Indexes and constraints
- RLS (Row Level Security) policies
- Row counts and data samples
- Relationship diagrams

#### Output Format

```markdown
## Database Schema

### Relevant Tables

#### users
| Column | Type | Constraints |
|--------|------|-------------|
| id | uuid | PRIMARY KEY |
| email | text | UNIQUE, NOT NULL |
| created_at | timestamptz | DEFAULT now() |

#### sessions
| Column | Type | Constraints |
|--------|------|-------------|
| id | uuid | PRIMARY KEY |
| user_id | uuid | REFERENCES users(id) |
| expires_at | timestamptz | NOT NULL |

### Relationships
```
users 1 ──── * sessions
      │
      └──── * profiles
```

### RLS Policies
- `users_select_own`: Users can only see their own row
- `sessions_user_only`: Sessions filtered by user_id = auth.uid()

### Advisories
⚠️ Table `logs` has no index on `created_at` (slow queries)
```

#### Usage Example

```
Claude: "I'm launching explore-db to understand the user schema..."

Agent explore-db:
→ list_tables
→ execute_sql: SELECT * FROM users LIMIT 5
→ execute_sql: SELECT * FROM pg_policies WHERE tablename = 'users'
→ get_advisors

Return: "Tables users, sessions, profiles. Relationships established. RLS configured..."
```

---

### explore-docs

**Purpose:** Retrieve up-to-date documentation for libraries and frameworks.

| Property | Value |
|----------|-------|
| **File** | `.claude/agents/explore-docs.md` |
| **Model** | Haiku |
| **Tools** | Context7 (MCP), WebSearch, WebFetch |
| **Mode** | Read-only |

#### Search Priority

```
1. Context7 (priority)
   └── resolve-library-id → query-docs

2. Fallback: WebSearch + WebFetch
   └── For libs not indexed in Context7
```

#### Why Context7 First?

Context7 is a service that indexes documentation for popular libraries. Advantages:

- Structured and up-to-date documentation
- Extracted code examples
- No noise (no marketing, no blogs)
- Precise responses

#### What It Filters

**Keeps:**
- Code examples and usage patterns
- API specifications and signatures
- Configuration options
- Error handling patterns
- Best practices

**Removes:**
- Marketing content
- Redundant explanations
- Deprecated information

#### Output Format

```markdown
## Documentation: React Query v5

### useMutation - Optimistic Updates

```typescript
const mutation = useMutation({
  mutationFn: updateTodo,
  onMutate: async (newTodo) => {
    // Cancel outgoing refetches
    await queryClient.cancelQueries({ queryKey: ['todos'] })

    // Snapshot previous value
    const previousTodos = queryClient.getQueryData(['todos'])

    // Optimistically update
    queryClient.setQueryData(['todos'], (old) => [...old, newTodo])

    return { previousTodos }
  },
  onError: (err, newTodo, context) => {
    // Rollback on error
    queryClient.setQueryData(['todos'], context.previousTodos)
  },
})
```

### Key Points
- `onMutate` for optimistic update
- Return context for rollback
- `onError` to restore previous state

### Source
Context7: /tanstack/query
```

#### Usage Example

```
Claude: "I'm launching explore-docs for React Query optimistic updates docs..."

Agent explore-docs:
→ resolve-library-id("tanstack-query")
→ query-docs("/tanstack/query", "useMutation optimistic updates")

Return: "Optimistic update pattern with onMutate, rollback on error..."
```

---

### websearch

**Purpose:** Quick web search for technical questions, libraries, APIs.

| Property | Value |
|----------|-------|
| **File** | `.claude/agents/websearch.md` |
| **Model** | Haiku |
| **Tools** | `WebSearch`, `WebFetch` |
| **Mode** | Read-only |

#### Workflow

1. **WebSearch** with precise keywords
2. **WebFetch** on the most relevant results
3. **Summary** of key information

#### When to Use vs explore-docs

| Case | Agent to use |
|------|--------------|
| React Query docs | explore-docs (Context7) |
| Stripe API docs | websearch (external API) |
| "Best practices auth 2025" | websearch |
| Next.js App Router docs | explore-docs (Context7) |
| Specific rare error | websearch |

#### Output Format

```markdown
## Results: Stripe Webhook Verification 2025

### Summary
Stripe recommends verifying webhooks with `stripe.webhooks.constructEvent()`.

### Key Points
- Always verify the signature
- Use raw body (not parsed)
- Different webhook secret per environment

### Example
```javascript
const event = stripe.webhooks.constructEvent(
  req.rawBody,
  req.headers['stripe-signature'],
  process.env.STRIPE_WEBHOOK_SECRET
);
```

### Sources
- https://stripe.com/docs/webhooks/signatures
- https://stripe.com/docs/webhooks/best-practices
```

---

### backend-code-optimizer

**Purpose:** Analyze and optimize Go backend code for quality, performance, and Clean Architecture adherence.

| Property | Value |
|----------|-------|
| **File** | `.claude/agents/backend-code-optimizer.md` |
| **Model** | Opus (more capable) |
| **Tools** | `Read`, `Grep`, `Glob` |
| **Mode** | Read-only |

#### What It Analyzes

- Code duplication and redundant logic
- Unused functions and variables
- Clean Architecture principle violations
- Parallelization opportunities with goroutines
- Performance inefficiencies
- Possible simplifications (deep nesting, complex logic)

#### What It Produces

```markdown
## Quality Analysis - Backend

### Critical Issues (fix immediately)
- `backend/internal/services/user.go:45` - Clean Architecture violation
- `backend/internal/repositories/cache.go:78` - Goroutine memory leak

### Important Issues (should be fixed)
- `backend/cmd/main.go:12` - 5 hardcoded parameters

### Nice-to-have (optional)
- `backend/internal/domain/user.go:23` - Possible simplification
```

#### Usage

This agent is automatically launched by the `/review` command when backend code is modified.

---

### frontend-code-reviewer

**Purpose:** Analyze and optimize React/TypeScript/Next.js frontend code for quality and performance.

| Property | Value |
|----------|-------|
| **File** | `.claude/agents/frontend-code-reviewer.md` |
| **Model** | Opus |
| **Tools** | `Read`, `Grep`, `Glob` |
| **Mode** | Read-only |

#### What It Analyzes

- React best practices (composition, hooks, rendering)
- TypeScript safety (any types, generics, discriminated unions)
- Next.js optimization (server/client components, data fetching)
- Performance (bundle size, lazy loading, code splitting)
- CSS architecture and accessibility
- Simplification opportunities

#### What It Produces

```markdown
## Quality Analysis - Frontend

### Critical Issues
- `frontend/src/pages/dashboard.tsx:12` - 3 useState that should be a useReducer
- `frontend/src/components/Form.tsx:45` - Excessive prop drilling

### Important Issues
- `frontend/src/hooks/useApi.ts:23` - Missing error boundary
- `frontend/src/components/Card.tsx:78` - Type 'any' used

### Performance Optimizations
- Bundle size: 245KB → 189KB (lazy loading modals)
- Unnecessary re-renders detected in ProductList

### Recommended Simplifications
- Lines 45-67 can be condensed with a custom hook
```

#### Usage

This agent is automatically launched by the `/review` command when frontend code is modified.

---

## Commands

Commands are **complete workflows** invocable with `/name`. They orchestrate agents and follow a structured cycle.

### Cycle Common to All Commands

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   EXPLORE   │ ──▶ │    PLAN     │ ──▶ │  VALIDATE   │
│  (Agents    │     │  (Analysis  │     │  (User      │
│   in //)    │     │   + plan)   │     │   approval) │
└─────────────┘     └─────────────┘     └──────┬──────┘
                                               │
                    ┌─────────────┐     ┌──────▼──────┐
                    │   VERIFY    │ ◀── │  IMPLEMENT  │
                    │  (Build +   │     │  (Code)     │
                    │   tests)    │     │             │
                    └─────────────┘     └─────────────┘
```

---

### commits

**Command:** `/commits`

**Purpose:** Automatic commit and push following Conventional Commits.

| Property | Value |
|----------|-------|
| **File** | `.claude/commands/commits.md` |
| **Model** | Haiku |
| **Allowed tools** | Bash (git only) |

#### Workflow

1. **Stage** - `git add -A`
2. **Analyze** - `git diff --cached --stat`
3. **Message** - Generation according to Conventional Commits
4. **Push** - Immediate `git push`

#### Commit Types and SemVer

| Type | Description | SemVer Impact |
|------|-------------|---------------|
| `feat` | New feature | MINOR (1.x.0) |
| `fix` | Bug fix | PATCH (1.0.x) |
| `docs` | Documentation | None |
| `style` | Formatting (no logic) | None |
| `refactor` | Refactoring (no feature/fix) | None |
| `perf` | Performance improvement | None |
| `test` | Adding/fixing tests | None |
| `build` | Build system | None |
| `ci` | CI configuration | None |
| `chore` | Maintenance | None |

#### Message Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

**Rules:**
- Subject: max 50 characters
- Imperative mood ("Add" not "Added")
- Capitalize after colon
- Body: wrap at 72 characters

#### Examples

```bash
feat: Add user authentication
fix(api): Handle timeout errors gracefully
feat(auth): Add OAuth2 support
feat!: Change API response format  # Breaking change
```

#### Usage

```bash
# In Claude Code
> /commits

# Claude will:
# 1. git add -A
# 2. Analyze changes
# 3. Generate: "feat(auth): Add JWT middleware"
# 4. git commit -m "..."
# 5. git push
```

---

### dev

**Command:** `/dev spec="..." phase="..."`

**Purpose:** Execute a development phase from a spec file.

| Property | Value |
|----------|-------|
| **File** | `.claude/commands/dev.md` |
| **Arguments** | `spec` (required), `phase` (required), `done` (optional) |

#### Arguments

| Argument | Description | Example |
|----------|-------------|---------|
| `spec` | Path to the .md spec file | `specs/auth-feature.md` |
| `phase` | Phase number(s) to execute | `2` or `2-3` |
| `done` | Already completed phases | `0-1` |

#### Workflow (7 steps)

1. **UNDERSTAND** - Read spec, identify scope (backend/frontend/both)
2. **EXPLORE** (PARALLEL) - Launch agents according to scope
3. **POST-EXPLORATION** - Check completeness, launch missing agents
4. **SHOW PLAN** - Display enriched plan with files and patterns
5. **VALIDATE** - Request user approval
6. **IMPLEMENT** - Code following architecture order
7. **VERIFY** - Build and DB verification

#### Implementation Order

**Backend:**
```
Domain → Application → Infrastructure → Presentation
```

**Frontend:**
```
Types → API → Hooks → Components → Pages
```

#### Usage

```bash
# Execute phase 2 of my feature
> /dev spec="KANBAN_FEATURE.md" phase="2"

# Execute phases 3 and 4
> /dev spec="AUTH_FEATURE.md" phase="3-4" done="1-2"
```

---

### explore

**Command:** `/explore question="..."`

**Purpose:** Deep read-only investigation to answer a question.

| Property | Value |
|----------|-------|
| **File** | `.claude/commands/explore.md` |
| **Arguments** | `question` (required) |

#### Workflow

1. **PARSE** - Extract keywords, determine scope
2. **CLARIFY** - If ambiguous, ask user for clarification
3. **EXPLORE** (PARALLEL) - Launch relevant agents
4. **POST-EXPLORATION** - Check completeness
5. **ANSWER** - Complete response with evidence

#### Ultra Think Strategy

The agent reflects on completeness between each major step:

```
"Have I explored all aspects?"
"Are there files I might have missed?"
"Are the results consistent with each other?"
```

#### Output Format

```markdown
## Answer

[Direct and concise answer]

### Evidence
- `backend/auth/jwt.go:45` - Token validation
- `backend/middleware/auth.go:12` - Middleware chain

### Identified Patterns
- Repository pattern for DB
- Clean Architecture respected

### Documentation Consulted
- Fiber JWT middleware (Context7)

### Sources
- Context7: /gofiber/fiber
```

#### Usage

```bash
> /explore question="How does the caching system work in this project?"

> /explore question="What are the API routes protected by auth?"

> /explore question="How are errors handled on the frontend?"
```

---

### hotfix

**Command:** `/hotfix issue="..."`

**Purpose:** Fix bugs or modify existing features.

| Property | Value |
|----------|-------|
| **File** | `.claude/commands/hotfix.md` |
| **Model** | Opus (more capable) |
| **Arguments** | `issue` (required) |

#### Why Opus?

Hotfixes often require deep understanding of existing code and fine analysis of edge cases. Opus is more capable for this type of task.

#### Workflow (8 steps)

1. **PARSE ISSUE** - Extract area, bug type, affected layers
2. **EXPLORE** (PARALLEL) - All relevant agents
3. **POST-EXPLORATION** - Check completeness
4. **ANALYZE** - Bug analysis with root cause and file:line references
5. **PLAN** - Fix strategy with risks and mitigations
6. **VALIDATE** - User approval
7. **IMPLEMENT** - Apply fix (backend first, then frontend)
8. **VERIFY** - Build + DB verification
9. **SUMMARY** - Final report

#### Analysis Format

```markdown
## Bug Analysis

### Description
The JWT middleware doesn't refresh expired tokens.

### Root Cause
`backend/internal/auth/jwt.go:78` - Expiration check doesn't account for grace period.

### Impact
- Users brutally disconnected
- Bad UX

### Proposed Fix
Add grace period check before rejection.
```

#### Usage

```bash
> /hotfix issue="Users are randomly disconnected after 10 minutes"

> /hotfix issue="Delete button doesn't work on mobile"

> /hotfix issue="500 error when uploading files > 5MB"
```

---

### oneshot

**Command:** `/oneshot task="..."`

**Purpose:** Quick implementation without a full spec file.

| Property | Value |
|----------|-------|
| **File** | `.claude/commands/oneshot.md` |
| **Arguments** | `task` (required) |

#### When to Use

- Small isolated features
- Quick prototypes
- Well-defined tasks not requiring elaborate specs

#### Workflow (6 steps)

1. **EXPLORE** (PARALLEL) - Backend/frontend/DB/libs in parallel
2. **POST-EXPLORATION** - Find all similar patterns
3. **CLARIFY** - Questions if required (error messages, defaults, validation)
4. **PLAN** - Detailed plan with implementation order
5. **VALIDATE** - Approval
6. **IMPLEMENT** - Execution with patterns from exploration
7. **VERIFY** - Build

#### Usage

```bash
> /oneshot task="Add a logout button in the header"

> /oneshot task="Add pagination on the product list"

> /oneshot task="Display the unread notification count"
```

---

### plan-feature

**Command:** `/plan-feature name="..."`

**Purpose:** Create a complete development plan for a new feature.

| Property | Value |
|----------|-------|
| **File** | `.claude/commands/plan-feature.md` |
| **Arguments** | `name` (required) |
| **Output** | `{NAME}_FEATURE.md` at root |

#### Workflow (5 steps)

1. **GATHER REQUIREMENTS** - Description, mockups, business rules, integrations
2. **CLARIFY DETAILS** - Error messages, defaults, validation, UX flows, permissions
3. **EXPLORE** (PARALLEL) - All agents
4. **POST-EXPLORATION** - Completeness
5. **VALIDATE ARCHITECTURE** - Architectural plan approval
6. **WRITE SPEC FILE** - Write complete spec file
7. **DELIVER** - Confirmation and next steps

#### Generated Spec File Structure

```markdown
# {FEATURE_NAME}_FEATURE.md

## Overview
- Objective
- Summary
- Tech stack

## Context and Motivation
- Why this feature
- Problem solved

## Functional Specifications
- Detailed behavior
- Edge cases

## Technical Architecture
- Files to create/modify
- Diagrams

## Database
- Migrations
- Columns
- RLS policies

## Backend Implementation
- Phase 1: Domain
- Phase 2: Infrastructure
- Phase 3: Application
- Phase 4: Presentation

## Frontend Implementation
- Phase 5: Types/API
- Phase 6: Hooks
- Phase 7: Components
- Phase 8: Pages

## Execution Plan
- [ ] Phase 1: ...
- [ ] Phase 2: ...

## Important Notes
- Compatibility
- Performance
- Security
```

#### Philosophy: Split as Much as Possible

The goal is to **split phases into atomic tasks**. The more granular, the better:

- Each phase must be executable independently
- Each phase must compile/work alone
- Fresh context per phase = better results

#### Backend Phase Rules

| Rule | Description |
|------|-------------|
| **Order** | Domain → Infrastructure → Application → Presentation |
| **Max 5 items per phase** | If more, split into sub-phases |
| **Separate CRUD and business** | CRUD usecases and business logic in different phases |
| **Independent compilation** | Each phase must compile alone |

**Split example:**
```
# Too heavy (> 5 items)
Phase 2: CreateUser, UpdateUser, DeleteUser, GetUser, ListUsers, ValidateEmail, SendWelcome

# Better (split)
Phase 2a: CreateUser, UpdateUser, DeleteUser, GetUser, ListUsers (CRUD)
Phase 2b: ValidateEmail, SendWelcome (Business logic)
```

#### Customization

These limits are **configurable**. You can modify `.claude/commands/plan-feature.md` to:
- Increase/decrease max per phase
- Change layer order
- Add custom phases

#### Usage

```bash
> /plan-feature name="user-dashboard"
# Generates: USER_DASHBOARD_FEATURE.md

> /plan-feature name="payment-integration"
# Generates: PAYMENT_INTEGRATION_FEATURE.md
```

---

### review

**Command:** `/review [options]`

**Purpose:** Automated code review with scope detection.

| Property | Value |
|----------|-------|
| **File** | `.claude/commands/review.md` |
| **Arguments** | All optional (see below) |

#### Arguments

| Argument | Description | Default |
|----------|-------------|---------|
| `mode` | `pushed` (last commit) or `local` (uncommitted) | local |
| `spec` | Path to spec/plan .md | - |
| `commit` | Hash or commit range | - |
| `brief` | Short description | - |
| `scope` | Force scope: `frontend`, `backend`, `all` | auto-detect |

#### Automatic Scope Detection

The command automatically analyzes changed files:

```
backend/**/*.go → Backend review
frontend/src/** → Frontend review
Both → Full review
```

#### Analysis Criteria

**Backend:**
- Duplications
- Clean Architecture violations
- Unused code
- context.Context issues
- Error handling
- Performance

**Frontend:**
- Duplications
- Pattern violations
- Unused code
- TypeScript issues
- i18n (next-intl)
- Accessibility
- Performance

#### Quality Scores

**Backend (100 points):**
- Code cleanliness: 25
- Clean Architecture: 25
- Error handling: 25
- Performance: 25

**Frontend (100 points):**
- Code correctness: 20
- TypeScript: 20
- Performance: 20
- i18n: 20
- Best practices: 20

#### Fix Categorization

| Category | Description |
|----------|-------------|
| **Critical** | Must be fixed (bugs, security) |
| **Important** | Should be fixed (maintainability) |
| **Nice-to-have** | Optional improvements |

#### Usage

```bash
# Review local changes
> /review

# Review last commit
> /review mode="pushed"

# Review against a spec
> /review spec="AUTH_FEATURE.md"

# Review a specific commit
> /review commit="abc123"

# Force frontend scope
> /review scope="frontend"
```

---

## Settings & Hooks

The `.claude/settings.json` file configures **hooks** - actions triggered on certain events.

### Structure

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "afplay /path/project/.claude/song/finish.mp3 &",
        "timeout": 5000
      }
    ],
    "Notification": [
      {
        "matcher": "idle_prompt",
        "command": "afplay /path/project/.claude/song/need-human.mp3 &",
        "timeout": 5000
      }
    ]
  }
}
```

### Hook Types

| Hook | Trigger | Typical usage |
|------|---------|---------------|
| `Stop` | Task completed | Sound/visual notification |
| `Notification` | According to `matcher` | Specific alerts |

### Matchers for Notification

| Matcher | Trigger |
|---------|---------|
| `idle_prompt` | Claude is waiting for user input |

### Parameters

| Parameter | Description |
|-----------|-------------|
| `command` | Shell command to execute |
| `timeout` | Timeout in ms (default: 5000) |
| `matcher` | (Notification) Trigger condition |

### Create Your Own Hooks

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "notify-send 'Claude Code' 'Task completed!' &",
        "timeout": 5000
      },
      {
        "command": "curl -X POST https://my-webhook.com/claude-done &",
        "timeout": 10000
      }
    ]
  }
}
```

---

## Audio System

The `.claude/song/` folder contains audio files for notifications.

### Provided Files

| File | Size | Usage |
|------|------|-------|
| `finish.mp3` | 210 KB | Plays when a task is completed |
| `need-human.mp3` | 74 KB | Plays when Claude waits for your input |

### Why Audio Notifications?

When you launch a long task (complete feature, review, etc.), you can do something else. Sounds alert you when:

- **finish.mp3**: Claude is done, you can see the result
- **need-human.mp3**: Claude needs your input to continue

### Configuration by OS

#### macOS (default)

```json
{
  "command": "afplay /path/to/file.mp3 &"
}
```

`afplay` is included in macOS by default.

#### Linux

```bash
# Install an audio player
sudo apt install mpv  # or ffplay, aplay, paplay

# Configuration
{
  "command": "mpv --no-video /path/to/file.mp3 &"
}
```

Alternatives:
- `ffplay -nodisp -autoexit file.mp3 &`
- `aplay file.wav &` (WAV only)
- `paplay file.mp3 &` (PulseAudio)

#### Windows (PowerShell)

```json
{
  "command": "powershell -c (New-Object Media.SoundPlayer 'C:\\path\\file.wav').PlaySync()"
}
```

Or with an external player:
```json
{
  "command": "start /min wmplayer C:\\path\\file.mp3"
}
```

### Replace Sounds

1. Place your `.mp3` files in `.claude/song/`
2. Update paths in `settings.json`

**Recommendations:**
- Short sounds (< 5 seconds)
- Moderate volume
- Distinct from each other

### Disable Sounds

Remove or comment out hooks in `settings.json`:

```json
{
  "hooks": {
  }
}
```

---

## Customization for Your Stack

This framework is **language and framework agnostic**. The patterns are universal.

### Basic Principle

Agents and commands work with **any stack** because they:

1. **Explore** your existing code (no assumption about structure)
2. **Search for patterns** in your project
3. **Adapt** to your conventions

### Adapt CLAUDE.md

#### Frontend: Next.js → Vue/Svelte/Angular

**Original (Next.js):**
```markdown
- **Frontend**: Next.js 16 (App Router), TypeScript, TailwindCSS, React Query, Shadcn UI
```

**Vue.js:**
```markdown
- **Frontend**: Vue 3 (Composition API), TypeScript, TailwindCSS, Pinia, PrimeVue
```

**Svelte:**
```markdown
- **Frontend**: SvelteKit 2, TypeScript, TailwindCSS, Svelte stores
```

**Angular:**
```markdown
- **Frontend**: Angular 17, TypeScript, TailwindCSS, NgRx, Angular Material
```

#### Backend: Go → Python/Rust/Node.js

**Original (Go):**
```markdown
- **Backend**: Go (Golang), Fiber, Clean Architecture
```

**Python:**
```markdown
- **Backend**: Python 3.12, FastAPI, Clean Architecture
```

**Rust:**
```markdown
- **Backend**: Rust, Axum, Clean Architecture
```

**Node.js:**
```markdown
- **Backend**: Node.js, NestJS, Clean Architecture
```

#### Database: Supabase → Others

**Original (Supabase):**
```markdown
- **Database**: Supabase (PostgreSQL), Redis
```

**Direct PostgreSQL:**
```markdown
- **Database**: PostgreSQL 16, Redis
```

**MySQL:**
```markdown
- **Database**: MySQL 8, Redis
```

**MongoDB:**
```markdown
- **Database**: MongoDB 7, Redis
```

### Adapt Agents

#### explore-db without Supabase

If you don't use Supabase, modify `.claude/agents/explore-db.md` to use your tools:

```markdown
# For direct PostgreSQL
Use Bash with `psql` in read-only:
- psql -c "SELECT * FROM information_schema.tables"
- psql -c "SELECT * FROM pg_indexes"

# For MongoDB
Use Bash with `mongosh`:
- mongosh --eval "db.getCollectionNames()"
- mongosh --eval "db.users.find().limit(5)"
```

### Universal Patterns to Keep

These patterns work with **any stack**:

| Pattern | Description | Adaptable to |
|---------|-------------|--------------|
| **Clean Architecture** | Layer separation | Any backend language |
| **Explore → Plan → Validate → Implement** | Dev workflow | Any project |
| **DRY** | No duplication | Universal |
| **External configuration** | No hardcode | Universal |
| **Exhaustive error handling** | Handle all cases | Universal |
| **i18n** | No hardcoded strings | Any frontend |

### Customization Checklist

- [ ] Update stack in `CLAUDE.md` (Project Overview section)
- [ ] Adapt example paths (`frontend/src/`, `backend/internal/`)
- [ ] Modify build commands if different (`npm run dev`, `go run`, etc.)
- [ ] Adapt `explore-db.md` if not using Supabase
- [ ] Update audio paths in `settings.json`
- [ ] Adapt naming conventions if different
- [ ] Add your own libraries in Context7 examples

---

## Philosophy & Design Patterns

### DRY (Don't Repeat Yourself)

**Before creating** anything, Claude is instructed to:

1. Search for existing in the project
2. Check established patterns
3. Reuse and adapt

This avoids duplication and maintains consistency.

### Explore → Plan → Validate → Implement → Verify

Each command follows this cycle:

```
EXPLORE    → Collect context (agents in parallel)
     ↓
PLAN       → Analyze and plan
     ↓
VALIDATE   → Get user approval
     ↓
IMPLEMENT  → Code
     ↓
VERIFY     → Test (build, tests, DB)
```

**Why?**
- No implementation without complete context
- User keeps control
- Systematic verification

### Ultra Think Strategy

At each major transition, Claude reflects:

```
"Have I explored all relevant aspects?"
"Are there edge cases I missed?"
"Is my plan consistent with existing code?"
```

### Clean Architecture

Strict separation of responsibilities:

```
┌─────────────────────────────────────────┐
│            PRESENTATION                  │
│  (HTTP handlers, CLI, API)              │
├─────────────────────────────────────────┤
│            APPLICATION                   │
│  (Use cases, orchestration)             │
├─────────────────────────────────────────┤
│            INFRASTRUCTURE               │
│  (DB, external services, implementations)│
├─────────────────────────────────────────┤
│            DOMAIN                        │
│  (Entities, business rules, interfaces) │
└─────────────────────────────────────────┘
```

Dependencies go inward (Domain depends on nothing).

### Mandatory User Validation

Claude **never codes** without prior approval for non-trivial tasks. This guarantees:

- Alignment with expectations
- No surprises
- Correction opportunity before implementation

---

## Troubleshooting

### Sounds Don't Play

**macOS:**
```bash
# Test manually
afplay .claude/song/finish.mp3

# Check permissions
# System Preferences → Security & Privacy → Privacy → Microphone
```

**Linux:**
```bash
# Install mpv if not present
sudo apt install mpv

# Test
mpv --no-video .claude/song/finish.mp3
```

### Agents Not Recognized

Verify that:
1. The `.claude/agents/` folder exists
2. The `.md` files have the correct YAML frontmatter format
3. You are in the correct project directory

### Commands Don't Work

```bash
# Check structure
ls -la .claude/commands/

# Files must have .md extension
# and correct YAML frontmatter format
```

### Context7 Can't Find My Library

Fallback to WebSearch:
```
# In your prompt to Claude
"Use WebSearch to find docs for [library]"
```

### Claude Ignores My Guidelines

Verify that:
1. `CLAUDE.md` is at the project root
2. The file is well formatted (no markdown syntax errors)
3. Restart Claude Code to reload instructions

---

## Contributing

Contributions are welcome!

### How to Contribute

1. **Fork** the repository
2. **Create** a branch (`git checkout -b feature/my-feature`)
3. **Commit** your changes (`git commit -m 'feat: Add my feature'`)
4. **Push** (`git push origin feature/my-feature`)
5. **Open** a Pull Request

### Guidelines

- Follow existing conventions
- Document new features
- Test your changes
- One PR = one feature/fix

### Contribution Ideas

- New specialized agents
- New workflow commands
- Support for other databases
- Translations
- Documentation improvements

---

## License

MIT License - see [LICENSE](LICENSE)

```
MIT License

Copyright (c) 2026 Aurealibe

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
```

---

## Author

**[@Aurealibe](https://github.com/Aurealibe)**

---

*Made with Claude Code*
