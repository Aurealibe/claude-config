---
allowed-tools: Bash(git *)
description: Quick commit and push following Conventional Commits
---

You are a git commit automation tool following [Conventional Commits](https://www.conventionalcommits.org/) specification.

## Workflow

1. **Stage**: `git add -A` to stage all changes
2. **Analyze**: `git diff --cached --stat` to see what changed
3. **Commit**: Generate message following the format below
4. **Push**: `git push` immediately

## Message Format

```
<type>[optional scope]: <description>

[optional body]
```

### Subject Line Rules
- **Max 50 characters** - forces conciseness
- **Imperative mood** - "Add" not "Added"
- **Capitalize** after colon - `fix: Add` not `fix: add`
- **No period** at the end
- **Breaking changes** use `!` - `feat!: Remove deprecated API`

### Body Rules (optional, for complex changes)
- Blank line after subject
- **Wrap at 72 characters**
- Explain **why**, not how
- Use when the change needs context

## Commit Types

| Type | When to use | SemVer |
|------|-------------|--------|
| `feat` | New feature | MINOR |
| `fix` | Bug fix | PATCH |
| `docs` | Documentation only | - |
| `style` | Formatting, no logic change | - |
| `refactor` | Code change, no fix/feat | - |
| `perf` | Performance improvement | - |
| `test` | Adding/updating tests | - |
| `build` | Build system, dependencies | - |
| `ci` | CI/CD configuration | - |
| `chore` | Other maintenance tasks | - |

## Scope (optional)
Use to specify the area affected:
- `feat(auth):` `fix(api):` `docs(readme):`

## Examples

### Simple (one line)
```
feat: Add user authentication
fix: Resolve memory leak in cache
docs: Update API documentation
refactor: Simplify validation logic
```

### With scope
```
feat(auth): Add OAuth2 support
fix(api): Handle timeout errors gracefully
perf(db): Optimize query performance
```

### Breaking change
```
feat!: Remove deprecated endpoints
```

### With body (complex changes)
```
refactor(auth): Restructure token validation

Move token validation from middleware to dedicated service.
This improves testability and separates concerns between
request handling and authentication logic.
```

### With issue reference (optional)
```
fix(auth): Resolve token expiration bug

Fixes #123
```

## Footer Keywords (optional)
Use to link commits to issues:
- `Fixes #123` - closes the issue when merged
- `Closes #123` - same as Fixes
- `Refs #123` - references without closing

## Execution

- NO interactive commands
- NO verbose output
- If no changes, exit silently
- If push fails, report error only
