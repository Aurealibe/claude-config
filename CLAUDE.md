# AUREA Developer Guidelines

## Project Overview

**AUREA** Claude Code stack (Next.js 16 + Go).

- **Frontend**: Next.js 16 (App Router), TypeScript, TailwindCSS, React Query, Shadcn UI
- **Backend**: Go (Golang), Fiber, Clean Architecture
- **Database**: Supabase (PostgreSQL), Redis
- **Infrastructure**: Docker

### Supabase Project IDs

- **Dev**: `dev_project_id`
- **Prod**: `prod_project_id`

---

## Fundamental Development Principles

### 1. Database Interaction via MCP Supabase (PRIORITY)

**All database interactions MUST use MCP Supabase servers:**

- **Read/Write**: Use `mcp__supabase-dev__execute_sql` or `mcp__supabase-prod__execute_sql`
- **Migrations**: Use `mcp__supabase-dev__apply_migration` or `mcp__supabase-prod__apply_migration`
- **Inspection**: Use MCP commands `list_tables`, `list_extensions`, `list_migrations`
- **NEVER**: Direct connections, psql, or other SQL clients

### 2. Maximum Reuse of Existing Code

**GOLDEN RULE**: Before creating anything new, ALWAYS:

1. Search for existing functionality in the project
2. Review components/functions in the same directory
3. Follow patterns established in similar files
4. Reuse and adapt instead of recreating

**Practical examples**:
- Creating a UI component → check `frontend/src/components/`
- Adding a hook → check `frontend/src/hooks/`
- Adding a backend service → check `backend/internal/application/usecases/`
- Adding a repository → check `backend/internal/domain/repositories/`

### 3. Dynamic & Modular Code

- All code must be fully dynamic and modular
- **NO hardcoded** parameters, thresholds, URLs, paths, or credentials
- Load all values from configuration files (`.env` for secrets, `app.yaml` for config)
- Business logic must adapt automatically to configuration changes

### 4. Clean Code & Maintenance

- **NEVER** leave unused code - ask for user approval before deletion
- **DRY Principle** - duplication is a liability
- **Concurrency**: Use goroutines (Go) or async functions (TypeScript) where beneficial, but preserve logical flow

### 5. API & Error Handling

- **ALWAYS** handle all error cases in API responses
- Never return 500 unless it's a genuine internal server error
- **NEVER** simplify external API calls without understanding their constraints (Stripe, Google, etc.)
- Never expose internal errors in API responses

### 6. Up-to-Date Information & Documentation

**ALWAYS use current information for libraries, APIs, and best practices.**

#### Priority Order for Documentation:

1. **MCP Context7** (`mcp__plugin_context7_context7__resolve-library-id` + `mcp__plugin_context7_context7__query-docs`)
   - Use FIRST for any library documentation (React, Next.js, Supabase, Fiber, etc.)
   - Provides indexed, structured documentation
   - Example: Before using a React Query pattern, query Context7 for latest API

2. **WebSearch** (`WebSearch` tool)
   - Use when Context7 doesn't have the library
   - Use for latest best practices and patterns (add "2024 2025" to queries)
   - Use for error messages and troubleshooting
   - Use for external API documentation (Stripe, Google, etc.)

3. **WebFetch** (`WebFetch` tool)
   - Use to fetch specific documentation pages found via WebSearch
   - Use for official API documentation URLs

#### When to Search for Documentation:

- **Before using any library feature** you're not 100% certain about
- **When implementing a new pattern** (auth, caching, state management, etc.)
- **When encountering an error** from an external library
- **When integrating external APIs** (always check current API version)
- **When the codebase pattern seems outdated** compared to current best practices

#### Examples:

```
# Check React Query v5 patterns
mcp__plugin_context7_context7__resolve-library-id(libraryName="tanstack-query")
mcp__plugin_context7_context7__query-docs(libraryId="/tanstack/query", query="useMutation optimistic updates")

# Check latest Supabase RLS patterns
WebSearch(query="Supabase RLS policies best practices 2025 2026")

# Check Fiber middleware patterns
mcp__plugin_context7_context7__query-docs(libraryId="/gofiber/fiber", query="middleware authentication JWT")
```

**NEVER assume library APIs haven't changed. Always verify.**

### 7. Internationalization

- **ALWAYS** use `next-intl` for all user-facing text
- Define messages in `frontend/messages/{language}.json`
- **NEVER** hardcode user-facing strings
- Write code in English first, then translate

### 8. Context Propagation (Go)

- **ALWAYS** use `context.Context` as first parameter for I/O operations
- Applies to: repository methods, use case Execute methods, service methods
- **NEVER** use `context.Background()` except at application entry point

### 9. UI Design Quality (Frontend)

Pour toute création de composant UI significatif (pages, modals, forms, dashboards, cards), utilise le skill `frontend-design:frontend-design` pour garantir un design distinctif et production-ready.

---

## Core Architecture

### Frontend (Next.js 16)

- **Pattern**: Hybrid Server/Client Components
  - **Server Components** (default): Layouts, initial data fetching, static UI
  - **Client Components** (`'use client'`): Interactivity, state, hooks, providers
- **I18n**: MUST use `next-intl`
- **Styling**: Tailwind CSS + Shadcn UI

### Backend (Go Clean Architecture)

**4 Layers - Strict Separation**:

1. **Domain** (`internal/domain`): Entities, Value Objects, Repository Interfaces. NO external deps.
2. **Application** (`internal/application`): Use Cases, DTOs, Ports. Orchestrates logic.
3. **Infrastructure** (`internal/infrastructure`): Repository implementations, external services (DB, AI, Auth).
4. **Presentation** (`internal/presentation`): HTTP Handlers (Fiber), Middleware.

### Database & Environment

- **Parameterized Queries**: All database operations MUST use parameterized queries
- **Supabase Remote ONLY**: Never use local Supabase
- **Docker**: NEVER restart manually during dev - use `air` for auto-reload
- **Configuration**: `.env` for secrets, `app.yaml` for application config

### Logging Strategy

- **Dev**: Verbose (Info level) with full data structures
- **Prod**: Clean (Info/Warn/Error)
- **Retries**: NEVER log `ERROR` on retryable failures - use `Warn`

### Security

- **Secrets**: NEVER commit `.env`
- **Auth**: JWT in `httpOnly` cookies, backend validates tokens

---

## Development Process

### Before Implementation

1. Understand the full context of the request
2. Identify affected components and services
3. Evaluate dependencies and side effects
4. Plan all steps using TodoWrite

### Ask Clarifying Questions

If not clearly defined, clarify:
- Where should the new code be placed?
- What level of input validation is expected?
- What error scenarios should be handled?
- Are there performance or scalability constraints?

### Validation with User

**Approval required at key checkpoints**:
1. Before starting major work
2. After preparing implementation plan
3. If architectural decisions are needed

---

## Naming Conventions

- **Go structs**: PascalCase
- **JS/TS**: camelCase for variables, PascalCase for components
- **Files**: kebab-case for frontend, snake_case for backend

---

## Common Commands

```bash
# Setup
npm run setup:all

# Start Dev (Docker + Backend + Redis)
npm run dev

# Backend only
cd backend
go run cmd/main.go
go test ./...

# Frontend only
cd frontend
npm run dev
```
