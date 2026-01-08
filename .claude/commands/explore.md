---
name: explore
description: Deep codebase exploration to answer specific questions
arguments:
  - name: question
    description: "The question to investigate"
    required: true
---

Read-only exploration task.

## Ultra Think Strategy

Ultra think before answering:
- After exploration results: reflect on completeness, identify gaps
- Before answering: consider multiple interpretations, ensure accuracy
- Cross-reference findings from different sources

---

## 1. PARSE QUESTION

Extract from `$ARGUMENTS.question`:
- Key terms and concepts to search
- Whether backend, frontend, or both
- Whether external documentation is needed

---

## 1.5 CLARIFY IF AMBIGUOUS

Use AskUserQuestion if the question could have multiple interpretations:
- "How does X work?" -> Which layer? What aspect?
- "Where is X handled?" -> At which level?
- "Find X" -> Find what exactly? (usage/definition/all)

If question is clear and specific, skip to section 2.

---

## 2. EXPLORE (PARALLEL)

Launch agents based on question scope (single message, parallel execution):

| Need | Agent | Prompt |
|------|-------|--------|
| Backend | explore-codebase | "[keywords] in backend/" |
| Frontend | explore-codebase | "[keywords] in frontend/src/" |
| Database | explore-db | "[env] - [tables/schema question]" |
| Library | explore-docs | "[library] [feature]" |
| External | websearch | "[topic]" |

---

## 2.5 POST-EXPLORATION CHECK

After agents return, verify:
1. Database info complete? If not -> launch explore-db
2. Library docs complete? If not -> launch explore-docs

---

## 3. ANSWER

Provide comprehensive response:

```markdown
## Answer

[Direct answer to the question]

## Evidence

### Code Found
- `path/to/file.ts:XX` - [description]
- `path/to/file.go:YY` - [description]

### Patterns Identified
- [Pattern 1]: [explanation]

### Documentation (if explore-docs used)
- [Library]: [relevant info]

### External Sources (if websearch used)
- [Title](URL) - [what we learned]

## Recommendations (if applicable)
- [Suggestion based on findings]
```

---

## Rules

- **PARALLEL** - launch relevant agents in ONE message
- **CITE** - always reference file:line
- **READ-ONLY** - do not modify anything
- **THOROUGH** - gather complete context before answering
