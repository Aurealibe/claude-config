# Claude Code Config

**Un framework de configuration complet pour Claude Code** - Transformez Claude en un assistant de développement autonome avec des agents spécialisés, des commandes de workflow et des notifications audio.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Author](https://img.shields.io/badge/Author-@Aurealibe-blue.svg)](https://github.com/Aurealibe)

---

## Introduction

### Qu'est-ce que Claude Code ?

[Claude Code](https://code.claude.com/docs/en/overview) est l'outil CLI officiel d'Anthropic qui permet d'utiliser Claude directement dans votre terminal. Il peut lire et modifier des fichiers, exécuter des commandes, naviguer dans votre codebase, et bien plus encore.

### Pourquoi ce repository ?

Ce repository fournit une **configuration avancée et extensible** pour Claude Code, comprenant :

- **4 Agents spécialisés** pour l'exploration parallèle (code, base de données, documentation, web)
- **7 Commandes de workflow** pour automatiser le cycle de développement complet
- **Système de notifications audio** pour savoir quand Claude a terminé ou attend votre input
- **Guidelines de développement** personnalisables pour votre stack technique
- **Patterns d'architecture** transférables à n'importe quel langage/framework

### Ce que ça apporte concrètement

| Sans cette config | Avec cette config |
|-------------------|-------------------|
| Claude explore séquentiellement | Exploration parallèle avec 4 agents spécialisés |
| Pas de workflow structuré | Workflow Explore → Plan → Validate → Implement → Verify |
| Vous devez surveiller Claude | Notifications audio quand Claude termine ou attend |
| Approche ad-hoc | Commandes standardisées (`/commits`, `/review`, `/hotfix`, etc.) |
| Claude peut inventer des patterns | Guidelines forcent la réutilisation du code existant |

---

## Best Practices - Ma Methodologie

Voici comment j'utilise cette configuration en production pour obtenir du **code production-ready** de maniere consistante.

### 1. Lancer Claude Code

```bash
# Mode normal (recommande)
claude

# Puis activez le mode "thinking" (ultrathink) pour de meilleurs resultats
```

**Option avancee (plus dangereux) :**
```bash
claude --dangerously-skip-permissions
```
Cette option desactive les confirmations de securite. Claude peut executer des commandes sans demander. A utiliser uniquement si vous savez ce que vous faites.

Le mode thinking permet a Claude de reflechir plus profondement avant d'agir.

### 2. Workflow recommande

```
┌─────────────────────────────────────────────────────────────┐
│  /plan-feature name="ma-feature"                            │
│  → Brainstorming + ecriture du fichier de spec              │
│  → Genere: MA_FEATURE_FEATURE.md                            │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  /dev spec="MA_FEATURE_FEATURE.md" phase="1"                │
│  → Developpe la phase 1 (ex: Domain layer)                  │
│  → /clear apres completion                                  │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  /dev spec="MA_FEATURE_FEATURE.md" phase="2" done="1"       │
│  → Developpe la phase 2 (ex: Infrastructure)                │
│  → /clear apres completion                                  │
└─────────────────────────────────────────────────────────────┘
                            ↓
                          ...
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  /review spec="MA_FEATURE_FEATURE.md"                       │
│  → Review du code contre la spec                            │
│  → Corrections si necessaire                                │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  /commits                                                   │
│  → Commit automatique (Conventional Commits)                │
│  → Push vers le repo                                        │
└─────────────────────────────────────────────────────────────┘
```

### 3. Regle d'or : Un contexte vide = meilleurs resultats

**CRITIQUE** : Ne jamais depasser le contexte de Claude.

- **Une phase de dev = un nouveau chat**
- Apres chaque session, faites `/clear` et repartez sur du neuf
- Privilegiez toujours un contexte vide

Pourquoi ? Claude performe mieux avec un contexte frais. Un contexte surcharge degrade la qualite des reponses.

### 4. Pourquoi cette methodologie > mode plan par defaut

Vous pouvez utiliser Claude Code avec le mode plan par defaut, mais ma methodologie est meilleure car :

| Mode plan par defaut | Cette methodologie |
|---------------------|-------------------|
| Agents internes non configurables | **Agents custom configurables** (ajoutez les votres) |
| Exploration sequentielle | **Exploration parallele** (docs + code + db en meme temps) |
| Plan une seule fois au debut | Planification a CHAQUE commande |
| Pas de validation intermediaire | Validation utilisateur obligatoire avant chaque implementation |
| Contexte qui s'accumule | Contexte frais a chaque phase |
| Resultats variables | Resultats consistants |

**La vraie plus-value : les agents en parallele.**

Quand vous lancez une commande (`/dev`, `/hotfix`, `/explore`...), elle lance automatiquement plusieurs agents en parallele :

```
┌─────────────────────────────────────────────────────────────┐
│                    VOTRE COMMANDE                           │
│                                                             │
│   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│   │ explore-    │  │ explore-db  │  │ explore-    │        │
│   │ codebase    │  │             │  │ docs        │        │
│   │             │  │  (schema,   │  │             │        │
│   │ (patterns,  │  │   tables,   │  │ (librairies,│        │
│   │  code       │  │   RLS)      │  │  APIs)      │        │
│   │  existant)  │  │             │  │             │        │
│   └──────┬──────┘  └──────┬──────┘  └──────┬──────┘        │
│          │                │                │                │
│          └────────────────┼────────────────┘                │
│                           ▼                                 │
│              Contexte complet en quelques secondes          │
└─────────────────────────────────────────────────────────────┘
```

- **Plus rapide** : 3 agents en parallele vs exploration sequentielle
- **Meilleurs resultats** : contexte complet avant toute action
- **Configurable** : ajoutez vos propres agents dans `.claude/agents/`

Le mode plan par defaut de Claude utilise ses propres agents internes, plus lents et non modifiables. Avec cette config, vous controlez exactement ce que Claude explore.

### 5. Resume

```bash
# 1. Planifier la feature
/plan-feature name="user-auth"

# 2. Dev phase par phase (nouveau chat a chaque fois)
/dev spec="USER_AUTH_FEATURE.md" phase="1"
# → /clear

/dev spec="USER_AUTH_FEATURE.md" phase="2" done="1"
# → /clear

# 3. Review finale
/review spec="USER_AUTH_FEATURE.md"

# 4. Commit
/commits
```

Le but est de tendre facilement vers du **code de production**. La configuration est minimale, mais les resultats sont consistants.

---

## Table des matières

- [Quick Start](#quick-start)
- [Structure du Repository](#structure-du-repository)
- [CLAUDE.md - Le fichier de Guidelines](#claudemd---le-fichier-de-guidelines)
- [Agents](#agents)
  - [explore-codebase](#explore-codebase)
  - [explore-db](#explore-db)
  - [explore-docs](#explore-docs)
  - [websearch](#websearch)
- [Best Practices - Ma Methodologie](#best-practices---ma-methodologie)
- [Commands](#commands)
  - [commits](#commits)
  - [dev](#dev)
  - [explore](#explore)
  - [hotfix](#hotfix)
  - [oneshot](#oneshot)
  - [plan-feature](#plan-feature)
  - [review](#review)
- [Settings & Hooks](#settings--hooks)
- [Systeme Audio](#systeme-audio)
- [Personnalisation pour Votre Stack](#personnalisation-pour-votre-stack)
- [Philosophie & Design Patterns](#philosophie--design-patterns)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

---

## Quick Start

### Prerequis

- [Claude Code](https://code.claude.com/docs/en/overview) installe et configure
- Un projet existant ou nouveau
- (Optionnel) Pour les notifications audio : macOS avec `afplay`, ou equivalent Linux/Windows

### Installation

1. **Clonez ce repository** dans votre projet ou copiez le dossier `.claude/` :

```bash
# Option 1: Cloner le repo entier
git clone https://github.com/Aurealibe/claude-config.git
cp -r claude-config/.claude/ votre-projet/.claude/
cp claude-config/CLAUDE.md votre-projet/CLAUDE.md

# Option 2: Ajouter comme submodule (pour garder les mises a jour)
cd votre-projet
git submodule add https://github.com/Aurealibe/claude-config.git .claude-config
ln -s .claude-config/.claude .claude
ln -s .claude-config/CLAUDE.md CLAUDE.md
```

2. **Configurez les chemins audio** dans `.claude/settings.json` :

```json
{
  "hooks": {
    "Stop": [{
      "command": "afplay /chemin/vers/votre/projet/.claude/song/finish.mp3 &"
    }],
    "Notification": [{
      "matcher": "idle_prompt",
      "command": "afplay /chemin/vers/votre/projet/.claude/song/need-human.mp3 &"
    }]
  }
}
```

3. **Personnalisez `CLAUDE.md`** pour votre stack (voir [Personnalisation](#personnalisation-pour-votre-stack))

### Premier usage

```bash
# Lancez Claude Code dans votre projet
cd votre-projet
claude

# Testez une commande
> /explore question="Comment fonctionne l'authentification dans ce projet ?"

# Ou lancez un dev workflow
> /dev spec="specs/ma-feature.md" phase="1"
```

---

## Structure du Repository

```
claude-config/
├── .claude/                    # Configuration Claude Code
│   ├── agents/                 # 4 agents d'exploration specialises
│   │   ├── explore-codebase.md # Recherche dans le code source
│   │   ├── explore-db.md       # Exploration base de donnees Supabase
│   │   ├── explore-docs.md     # Documentation des librairies
│   │   └── websearch.md        # Recherche web rapide
│   │
│   ├── commands/               # 7 commandes de workflow
│   │   ├── commits.md          # Git commit automatique (Conventional Commits)
│   │   ├── dev.md              # Developpement par phases
│   │   ├── explore.md          # Investigation profonde
│   │   ├── hotfix.md           # Correction de bugs
│   │   ├── oneshot.md          # Implementation rapide
│   │   ├── plan-feature.md     # Planification de feature complete
│   │   └── review.md           # Revue de code automatisee
│   │
│   ├── song/                   # Fichiers audio pour notifications
│   │   ├── finish.mp3          # Joue quand une tache est terminee
│   │   └── need-human.mp3      # Joue quand Claude attend votre input
│   │
│   └── settings.json           # Configuration des hooks Claude Code
│
├── CLAUDE.md                   # Guidelines de developpement (instructions pour Claude)
├── LICENSE                     # MIT License
└── README.md                   # Ce fichier
```

### Explication de chaque element

| Fichier/Dossier | Role |
|-----------------|------|
| `.claude/agents/` | Agents specialises lances en parallele pour collecter du contexte rapidement |
| `.claude/commands/` | Commandes invocables avec `/nom` qui orchestrent des workflows complets |
| `.claude/song/` | Sons de notification pour feedback audio |
| `.claude/settings.json` | Hooks qui declenchent des actions sur certains evenements |
| `CLAUDE.md` | Instructions que Claude lit au demarrage - definit vos conventions et patterns |

---

## CLAUDE.md - Le fichier de Guidelines

Le fichier `CLAUDE.md` a la racine de votre projet est **lu automatiquement par Claude Code** au demarrage. C'est votre moyen de definir :

- Les conventions de votre projet
- L'architecture a respecter
- Les patterns a reutiliser
- Les regles strictes (securite, i18n, etc.)

### Structure du CLAUDE.md fourni

```markdown
# Sections principales

1. Project Overview       → Stack technique, IDs de projet
2. Development Principles → Regles fondamentales (DB, reuse, clean code, etc.)
3. Core Architecture      → Layers backend, patterns frontend
4. Development Process    → Workflow avant implementation
5. Naming Conventions     → Standards de nommage
6. Common Commands        → Commandes utiles
```

### Section par section

#### 1. Project Overview

```markdown
## Project Overview

**AUREA** Claude Code stack (Next.js 16 + Go).

- **Frontend**: Next.js 16 (App Router), TypeScript, TailwindCSS, React Query, Shadcn UI
- **Backend**: Go (Golang), Fiber, Clean Architecture
- **Database**: Supabase (PostgreSQL), Redis
- **Infrastructure**: Docker
```

**Comment personnaliser :**

Remplacez par votre stack. Par exemple pour Vue + Python :

```markdown
## Project Overview

**MonProjet** - Stack Vue.js + Python.

- **Frontend**: Vue 3 (Composition API), TypeScript, TailwindCSS, Pinia
- **Backend**: Python, FastAPI, Clean Architecture
- **Database**: PostgreSQL, Redis
- **Infrastructure**: Docker
```

#### 2. Fundamental Development Principles

Ces principes sont **universels** et applicables a toute stack :

| Principe | Explication | Pourquoi c'est important |
|----------|-------------|--------------------------|
| **Database via MCP** | Utiliser les outils MCP pour les requetes DB | Tracabilite, securite, pas de connexions directes |
| **Maximum Reuse** | TOUJOURS chercher l'existant avant de creer | Evite la duplication, maintient la coherence |
| **Dynamic & Modular** | Pas de valeurs hardcodees | Flexibilite, configuration centralisee |
| **Clean Code** | Pas de code mort, DRY | Maintenabilite |
| **Error Handling** | Gerer TOUS les cas d'erreur | Robustesse |
| **Up-to-Date Docs** | Verifier la doc avant d'utiliser une lib | Evite les APIs deprecees |
| **i18n** | Pas de strings hardcodees | Internationalisation |
| **Context Propagation** | `context.Context` en Go | Tracing, cancellation, timeouts |

#### 3. Core Architecture

L'exemple fourni utilise **Clean Architecture** avec 4 layers :

```
Domain → Application → Infrastructure → Presentation
```

**Adaptable a toute stack :**

| Layer | Go (Fiber) | Python (FastAPI) | Node.js (NestJS) | Rust (Axum) |
|-------|------------|------------------|------------------|-------------|
| Domain | `internal/domain` | `app/domain` | `src/domain` | `src/domain` |
| Application | `internal/application` | `app/application` | `src/application` | `src/application` |
| Infrastructure | `internal/infrastructure` | `app/infrastructure` | `src/infrastructure` | `src/infrastructure` |
| Presentation | `internal/presentation` | `app/api` | `src/controllers` | `src/handlers` |

---

## Agents

Les agents sont des **sous-processus specialises** que Claude peut lancer en parallele pour collecter du contexte rapidement. Ils sont **read-only** (ne modifient rien) et retournent un rapport structure.

### Comment les agents fonctionnent

```
┌─────────────────────────────────────────────────────────────┐
│                    CLAUDE (Principal)                        │
│                                                              │
│  "Je dois comprendre comment fonctionne l'auth..."          │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │ explore-     │  │ explore-db   │  │ explore-     │       │
│  │ codebase     │  │              │  │ docs         │       │
│  │              │  │ "Tables      │  │              │       │
│  │ "Cherche     │  │  users,      │  │ "Doc JWT,    │       │
│  │  auth dans   │  │  sessions"   │  │  Fiber       │       │
│  │  backend/"   │  │              │  │  middleware" │       │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘       │
│         │                 │                 │                │
│         ▼                 ▼                 ▼                │
│  ┌──────────────────────────────────────────────────┐       │
│  │              RESULTATS COMBINES                   │       │
│  │  - 15 fichiers Go avec auth                      │       │
│  │  - Schema tables users/sessions                   │       │
│  │  - Patterns JWT Fiber v2                          │       │
│  └──────────────────────────────────────────────────┘       │
│                                                              │
│  → Claude a maintenant le contexte complet pour agir        │
└─────────────────────────────────────────────────────────────┘
```

---

### explore-codebase

**But :** Trouver du code existant, des patterns, des dependances dans votre projet.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/agents/explore-codebase.md` |
| **Modele** | Haiku (leger, rapide) |
| **Outils** | `Read`, `Grep`, `Glob` |
| **Mode** | Read-only |

#### Strategie de recherche

1. **Recherche large** - Grep pour trouver les points d'entree
2. **Recherches paralleles** - Mots-cles lies en meme temps
3. **Lecture complete** - Fichiers entiers avec contexte
4. **Suivi des imports** - Decouverte des dependances

#### Ce qu'il trouve

- Fonctions/classes/composants similaires existants
- Patterns etablis dans le projet
- Fichiers de configuration
- Schemas de base de donnees
- Endpoints API
- Tests et exemples d'utilisation
- Fonctions utilitaires reutilisables

#### Format de sortie

```markdown
## Resultats de l'exploration

### Fichiers pertinents
- `backend/internal/auth/jwt.go:45` - Middleware JWT existant
- `backend/internal/auth/session.go:12` - Gestion des sessions

### Patterns identifies
- Pattern Repository utilise pour tous les acces DB
- Middleware chain: Logger → Auth → RateLimit → Handler

### Code reutilisable
```go
// backend/internal/auth/jwt.go:45-67
func ValidateToken(ctx context.Context, token string) (*Claims, error) {
    // ... implementation existante
}
```

### Dependances
- `github.com/golang-jwt/jwt/v5` - Librairie JWT
- `backend/internal/config` - Configuration
```

#### Exemple d'utilisation

Quand la commande `/explore` ou `/dev` est lancee, cet agent est automatiquement invoque :

```
Claude: "Je lance explore-codebase pour chercher les patterns d'auth..."

Agent explore-codebase:
→ Grep "auth" dans backend/
→ Grep "middleware" dans backend/
→ Grep "jwt" dans backend/
→ Lecture complete des fichiers trouves
→ Suivi des imports

Retour: "Trouve 8 fichiers, pattern middleware etabli, JWT deja implemente..."
```

---

### explore-db

**But :** Explorer le schema de base de donnees Supabase (PostgreSQL).

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/agents/explore-db.md` |
| **Modele** | Haiku |
| **Outils** | MCP Supabase (`list_tables`, `execute_sql`, etc.) |
| **Mode** | Read-only (SELECT uniquement) |

#### Detection d'environnement

L'agent detecte automatiquement l'environnement depuis votre prompt :

```
"Explore la DB dev" → Utilise mcp__supabase-dev__*
"Explore la DB prod" → Utilise mcp__supabase-prod__*
(par defaut) → Utilise dev
```

#### Outils MCP disponibles

| Outil | Usage |
|-------|-------|
| `list_tables` | Liste toutes les tables avec colonnes |
| `list_extensions` | Extensions PostgreSQL actives |
| `list_migrations` | Historique des migrations |
| `execute_sql` | Requetes SELECT (read-only) |
| `get_advisors` | Alertes securite/performance |

#### Strategie de recherche

1. `list_tables` pour comprendre le schema global
2. `execute_sql` pour requetes ciblees sur les tables pertinentes
3. `get_advisors` pour identifier les problemes
4. Tracer les foreign keys pour comprendre les relations

#### Ce qu'il trouve

- Structure des tables et colonnes
- Relations foreign key
- Index et contraintes
- Politiques RLS (Row Level Security)
- Nombre de lignes et echantillons de donnees
- Diagrammes de relations

#### Format de sortie

```markdown
## Schema Database

### Tables pertinentes

#### users
| Colonne | Type | Contraintes |
|---------|------|-------------|
| id | uuid | PRIMARY KEY |
| email | text | UNIQUE, NOT NULL |
| created_at | timestamptz | DEFAULT now() |

#### sessions
| Colonne | Type | Contraintes |
|---------|------|-------------|
| id | uuid | PRIMARY KEY |
| user_id | uuid | REFERENCES users(id) |
| expires_at | timestamptz | NOT NULL |

### Relations
```
users 1 ──── * sessions
      │
      └──── * profiles
```

### RLS Policies
- `users_select_own`: Users ne peuvent voir que leur propre ligne
- `sessions_user_only`: Sessions filtrees par user_id = auth.uid()

### Advisories
⚠️ Table `logs` n'a pas d'index sur `created_at` (queries lentes)
```

#### Exemple d'utilisation

```
Claude: "Je lance explore-db pour comprendre le schema des utilisateurs..."

Agent explore-db:
→ list_tables
→ execute_sql: SELECT * FROM users LIMIT 5
→ execute_sql: SELECT * FROM pg_policies WHERE tablename = 'users'
→ get_advisors

Retour: "Tables users, sessions, profiles. Relations etablies. RLS configure..."
```

---

### explore-docs

**But :** Recuperer la documentation a jour des librairies et frameworks.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/agents/explore-docs.md` |
| **Modele** | Haiku |
| **Outils** | Context7 (MCP), WebSearch, WebFetch |
| **Mode** | Read-only |

#### Priorite de recherche

```
1. Context7 (prioritaire)
   └── resolve-library-id → query-docs

2. Fallback: WebSearch + WebFetch
   └── Pour libs non indexees dans Context7
```

#### Pourquoi Context7 en priorite ?

Context7 est un service qui indexe la documentation des librairies populaires. Avantages :

- Documentation structuree et a jour
- Exemples de code extraits
- Pas de bruit (pas de marketing, pas de blogs)
- Reponses precises

#### Ce qu'il filtre

**Garde :**
- Exemples de code et patterns d'usage
- Specifications d'API et signatures
- Options de configuration
- Patterns de gestion d'erreur
- Bonnes pratiques

**Elimine :**
- Contenu marketing
- Explications redondantes
- Informations deprecees

#### Format de sortie

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

### Points cles
- `onMutate` pour optimistic update
- Retourner le context pour rollback
- `onError` pour restaurer l'etat precedent

### Source
Context7: /tanstack/query
```

#### Exemple d'utilisation

```
Claude: "Je lance explore-docs pour la doc React Query optimistic updates..."

Agent explore-docs:
→ resolve-library-id("tanstack-query")
→ query-docs("/tanstack/query", "useMutation optimistic updates")

Retour: "Pattern optimistic update avec onMutate, rollback sur erreur..."
```

---

### websearch

**But :** Recherche web rapide pour questions techniques, librairies, APIs.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/agents/websearch.md` |
| **Modele** | Haiku |
| **Outils** | `WebSearch`, `WebFetch` |
| **Mode** | Read-only |

#### Workflow

1. **WebSearch** avec mots-cles precis
2. **WebFetch** sur les resultats les plus pertinents
3. **Resume** des informations cles

#### Quand l'utiliser vs explore-docs

| Cas | Agent a utiliser |
|-----|------------------|
| Doc React Query | explore-docs (Context7) |
| Doc Stripe API | websearch (API externe) |
| "Best practices auth 2025" | websearch |
| Doc Next.js App Router | explore-docs (Context7) |
| Erreur specifique rare | websearch |

#### Format de sortie

```markdown
## Resultats: Stripe Webhook Verification 2025

### Resume
Stripe recommande de verifier les webhooks avec `stripe.webhooks.constructEvent()`.

### Points cles
- Toujours verifier la signature
- Utiliser le raw body (pas parse)
- Secret webhook different par environnement

### Exemple
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

## Commands

Les commandes sont des **workflows complets** invocables avec `/nom`. Elles orchestrent les agents et suivent un cycle structure.

### Cycle commun a toutes les commandes

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   EXPLORE   │ ──▶ │    PLAN     │ ──▶ │  VALIDATE   │
│  (Agents    │     │  (Analyse   │     │  (Approval  │
│   en //)    │     │   + plan)   │     │   user)     │
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

**Commande :** `/commits`

**But :** Commit et push automatique en suivant Conventional Commits.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/commands/commits.md` |
| **Modele** | Haiku |
| **Outils autorises** | Bash (git uniquement) |

#### Workflow

1. **Stage** - `git add -A`
2. **Analyse** - `git diff --cached --stat`
3. **Message** - Generation selon Conventional Commits
4. **Push** - `git push` immediat

#### Types de commit et SemVer

| Type | Description | Impact SemVer |
|------|-------------|---------------|
| `feat` | Nouvelle fonctionnalite | MINOR (1.x.0) |
| `fix` | Correction de bug | PATCH (1.0.x) |
| `docs` | Documentation | Aucun |
| `style` | Formatage (pas de logique) | Aucun |
| `refactor` | Refactoring (pas de feature/fix) | Aucun |
| `perf` | Amelioration performance | Aucun |
| `test` | Ajout/correction de tests | Aucun |
| `build` | Systeme de build | Aucun |
| `ci` | Configuration CI | Aucun |
| `chore` | Maintenance | Aucun |

#### Format du message

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

**Regles :**
- Sujet : max 50 caracteres
- Mode imperatif ("Add" pas "Added")
- Majuscule apres les deux-points
- Body : wrap a 72 caracteres

#### Exemples

```bash
feat: Add user authentication
fix(api): Handle timeout errors gracefully
feat(auth): Add OAuth2 support
feat!: Change API response format  # Breaking change
```

#### Usage

```bash
# Dans Claude Code
> /commits

# Claude va:
# 1. git add -A
# 2. Analyser les changements
# 3. Generer: "feat(auth): Add JWT middleware"
# 4. git commit -m "..."
# 5. git push
```

---

### dev

**Commande :** `/dev spec="..." phase="..."`

**But :** Executer une phase de developpement depuis un fichier de spec.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/commands/dev.md` |
| **Arguments** | `spec` (requis), `phase` (requis), `done` (optionnel) |

#### Arguments

| Argument | Description | Exemple |
|----------|-------------|---------|
| `spec` | Chemin vers le fichier .md de spec | `specs/auth-feature.md` |
| `phase` | Numero(s) de phase a executer | `2` ou `2-3` |
| `done` | Phases deja completees | `0-1` |

#### Workflow (7 etapes)

1. **UNDERSTAND** - Lire la spec, identifier le scope (backend/frontend/both)
2. **EXPLORE** (PARALLELE) - Lancer les agents selon le scope
3. **POST-EXPLORATION** - Verifier la completude, lancer agents manquants
4. **SHOW PLAN** - Afficher le plan enrichi avec fichiers et patterns
5. **VALIDATE** - Demander approbation utilisateur
6. **IMPLEMENT** - Coder en suivant l'ordre d'architecture
7. **VERIFY** - Build et verification DB

#### Ordre d'implementation

**Backend :**
```
Domain → Application → Infrastructure → Presentation
```

**Frontend :**
```
Types → API → Hooks → Components → Pages
```

#### Usage

```bash
# Executer la phase 2 de ma feature
> /dev spec="KANBAN_FEATURE.md" phase="2"

# Executer les phases 3 et 4
> /dev spec="AUTH_FEATURE.md" phase="3-4" done="1-2"
```

---

### explore

**Commande :** `/explore question="..."`

**But :** Investigation profonde read-only pour repondre a une question.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/commands/explore.md` |
| **Arguments** | `question` (requis) |

#### Workflow

1. **PARSE** - Extraire mots-cles, determiner scope
2. **CLARIFY** - Si ambigu, demander precision a l'utilisateur
3. **EXPLORE** (PARALLELE) - Lancer agents pertinents
4. **POST-EXPLORATION** - Verifier completude
5. **ANSWER** - Reponse complete avec preuves

#### Ultra Think Strategy

L'agent reflechit a la completude entre chaque etape majeure :

```
"Est-ce que j'ai explore tous les aspects ?"
"Y a-t-il des fichiers que j'aurais pu manquer ?"
"Les resultats sont-ils coherents entre eux ?"
```

#### Format de sortie

```markdown
## Reponse

[Reponse directe et concise]

### Evidence
- `backend/auth/jwt.go:45` - Validation token
- `backend/middleware/auth.go:12` - Middleware chain

### Patterns identifies
- Repository pattern pour DB
- Clean Architecture respectee

### Documentation consultee
- Fiber JWT middleware (Context7)

### Sources
- Context7: /gofiber/fiber
```

#### Usage

```bash
> /explore question="Comment fonctionne le systeme de cache dans ce projet ?"

> /explore question="Quelles sont les routes API protegees par auth ?"

> /explore question="Comment les erreurs sont-elles gerees cote frontend ?"
```

---

### hotfix

**Commande :** `/hotfix issue="..."`

**But :** Corriger des bugs ou modifier des features existantes.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/commands/hotfix.md` |
| **Modele** | Opus (plus capable) |
| **Arguments** | `issue` (requis) |

#### Pourquoi Opus ?

Les hotfixes necessitent souvent une comprehension profonde du code existant et une analyse fine des edge cases. Opus est plus capable pour ce type de tache.

#### Workflow (8 etapes)

1. **PARSE ISSUE** - Extraire la zone, le type de bug, les layers affectes
2. **EXPLORE** (PARALLELE) - Tous les agents pertinents
3. **POST-EXPLORATION** - Verifier completude
4. **ANALYZE** - Analyse du bug avec root cause et references file:line
5. **PLAN** - Strategie de fix avec risques et mitigations
6. **VALIDATE** - Approbation utilisateur
7. **IMPLEMENT** - Appliquer le fix (backend first, puis frontend)
8. **VERIFY** - Build + verification DB
9. **SUMMARY** - Rapport final

#### Format d'analyse

```markdown
## Analyse du bug

### Description
Le middleware JWT ne rafraichit pas le token expire.

### Root Cause
`backend/internal/auth/jwt.go:78` - La verification d'expiration ne tient pas compte du grace period.

### Impact
- Users deconnectes brutalement
- Mauvaise UX

### Fix propose
Ajouter verification du grace period avant rejection.
```

#### Usage

```bash
> /hotfix issue="Les utilisateurs sont deconnectes aleatoirement apres 10 minutes"

> /hotfix issue="Le bouton de suppression ne marche pas sur mobile"

> /hotfix issue="Erreur 500 lors de l'upload de fichiers > 5MB"
```

---

### oneshot

**Commande :** `/oneshot task="..."`

**But :** Implementation rapide sans fichier de spec complet.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/commands/oneshot.md` |
| **Arguments** | `task` (requis) |

#### Quand l'utiliser

- Petites features isolees
- Prototypes rapides
- Taches bien definies ne necessitant pas de spec elaboree

#### Workflow (6 etapes)

1. **EXPLORE** (PARALLELE) - Backend/frontend/DB/libs en parallele
2. **POST-EXPLORATION** - Trouver tous les patterns similaires
3. **CLARIFY** - Questions si requis (messages d'erreur, defaults, validation)
4. **PLAN** - Plan detaille avec ordre d'implementation
5. **VALIDATE** - Approbation
6. **IMPLEMENT** - Execution avec patterns de l'exploration
7. **VERIFY** - Build

#### Usage

```bash
> /oneshot task="Ajouter un bouton de logout dans le header"

> /oneshot task="Ajouter la pagination sur la liste des produits"

> /oneshot task="Afficher le nombre de notifications non lues"
```

---

### plan-feature

**Commande :** `/plan-feature name="..."`

**But :** Creer un plan de developpement complet pour une nouvelle feature.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/commands/plan-feature.md` |
| **Arguments** | `name` (requis) |
| **Output** | `{NAME}_FEATURE.md` a la racine |

#### Workflow (5 etapes)

1. **GATHER REQUIREMENTS** - Description, mockups, regles business, integrations
2. **CLARIFY DETAILS** - Messages d'erreur, defaults, validation, UX flows, permissions
3. **EXPLORE** (PARALLELE) - Tous les agents
4. **POST-EXPLORATION** - Completude
5. **VALIDATE ARCHITECTURE** - Approbation du plan architectural
6. **WRITE SPEC FILE** - Ecriture du fichier de spec complet
7. **DELIVER** - Confirmation et prochaines etapes

#### Structure du fichier de spec genere

```markdown
# {FEATURE_NAME}_FEATURE.md

## Overview
- Objectif
- Resume
- Stack technique

## Context and Motivation
- Pourquoi cette feature
- Probleme resolu

## Functional Specifications
- Comportement detaille
- Edge cases

## Technical Architecture
- Fichiers a creer/modifier
- Diagrammes

## Database
- Migrations
- Colonnes
- Politiques RLS

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
- Compatibilite
- Performance
- Securite
```

#### Philosophie : Decouper au maximum

Le but est de **decouper les phases en taches atomiques**. Plus c'est granulaire, mieux c'est :

- Chaque phase doit etre executable independamment
- Chaque phase doit compiler/fonctionner seule
- Un contexte frais par phase = meilleurs resultats

#### Regles des phases Backend

| Regle | Description |
|-------|-------------|
| **Ordre** | Domain → Infrastructure → Application → Presentation |
| **Max 5 items par phase** | Si plus, splitter en sous-phases |
| **Separer CRUD et business** | Les usecases CRUD et business logic dans des phases differentes |
| **Compilation independante** | Chaque phase doit compiler seule |

**Exemple de split :**
```
# Trop charge (> 5 items)
Phase 2: CreateUser, UpdateUser, DeleteUser, GetUser, ListUsers, ValidateEmail, SendWelcome

# Mieux (splitte)
Phase 2a: CreateUser, UpdateUser, DeleteUser, GetUser, ListUsers (CRUD)
Phase 2b: ValidateEmail, SendWelcome (Business logic)
```

#### Personnalisation

Ces limites sont **configurables**. Vous pouvez modifier `.claude/commands/plan-feature.md` pour :
- Augmenter/diminuer le max par phase
- Changer l'ordre des layers
- Ajouter des phases custom

#### Usage

```bash
> /plan-feature name="user-dashboard"
# Genere: USER_DASHBOARD_FEATURE.md

> /plan-feature name="payment-integration"
# Genere: PAYMENT_INTEGRATION_FEATURE.md
```

---

### review

**Commande :** `/review [options]`

**But :** Revue de code automatisee avec detection de scope.

| Propriete | Valeur |
|-----------|--------|
| **Fichier** | `.claude/commands/review.md` |
| **Arguments** | Tous optionnels (voir ci-dessous) |

#### Arguments

| Argument | Description | Defaut |
|----------|-------------|--------|
| `mode` | `pushed` (dernier commit) ou `local` (non committe) | local |
| `spec` | Chemin vers spec/plan .md | - |
| `commit` | Hash ou range de commits | - |
| `brief` | Description courte | - |
| `scope` | Force le scope : `frontend`, `backend`, `all` | auto-detect |

#### Detection automatique du scope

La commande analyse automatiquement les fichiers changes :

```
backend/**/*.go → Backend review
frontend/src/** → Frontend review
Les deux → Full review
```

#### Criteres d'analyse

**Backend :**
- Duplications
- Violations Clean Architecture
- Code inutilise
- Problemes de context.Context
- Gestion d'erreurs
- Performance

**Frontend :**
- Duplications
- Violations de patterns
- Code inutilise
- Problemes TypeScript
- i18n (next-intl)
- Accessibilite
- Performance

#### Scores de qualite

**Backend (100 points) :**
- Code cleanliness: 25
- Clean Architecture: 25
- Error handling: 25
- Performance: 25

**Frontend (100 points) :**
- Code correctness: 20
- TypeScript: 20
- Performance: 20
- i18n: 20
- Best practices: 20

#### Categorisation des fixes

| Categorie | Description |
|-----------|-------------|
| **Critical** | Doit etre corrige (bugs, securite) |
| **Important** | Devrait etre corrige (maintenabilite) |
| **Nice-to-have** | Ameliorations optionnelles |

#### Usage

```bash
# Review des changements locaux
> /review

# Review du dernier commit
> /review mode="pushed"

# Review contre une spec
> /review spec="AUTH_FEATURE.md"

# Review d'un commit specifique
> /review commit="abc123"

# Forcer le scope frontend
> /review scope="frontend"
```

---

## Settings & Hooks

Le fichier `.claude/settings.json` configure les **hooks** - des actions declenchees sur certains evenements.

### Structure

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "afplay /chemin/projet/.claude/song/finish.mp3 &",
        "timeout": 5000
      }
    ],
    "Notification": [
      {
        "matcher": "idle_prompt",
        "command": "afplay /chemin/projet/.claude/song/need-human.mp3 &",
        "timeout": 5000
      }
    ]
  }
}
```

### Types de hooks

| Hook | Declencheur | Usage typique |
|------|-------------|---------------|
| `Stop` | Tache terminee | Notification sonore/visuelle |
| `Notification` | Selon le `matcher` | Alertes specifiques |

### Matchers pour Notification

| Matcher | Declencheur |
|---------|-------------|
| `idle_prompt` | Claude attend un input utilisateur |

### Parametres

| Parametre | Description |
|-----------|-------------|
| `command` | Commande shell a executer |
| `timeout` | Timeout en ms (defaut: 5000) |
| `matcher` | (Notification) Condition de declenchement |

### Creer ses propres hooks

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "notify-send 'Claude Code' 'Tache terminee!' &",
        "timeout": 5000
      },
      {
        "command": "curl -X POST https://mon-webhook.com/claude-done &",
        "timeout": 10000
      }
    ]
  }
}
```

---

## Systeme Audio

Le dossier `.claude/song/` contient les fichiers audio pour les notifications.

### Fichiers fournis

| Fichier | Taille | Usage |
|---------|--------|-------|
| `finish.mp3` | 210 KB | Joue quand une tache est terminee |
| `need-human.mp3` | 74 KB | Joue quand Claude attend votre input |

### Pourquoi des notifications audio ?

Quand vous lancez une tache longue (feature complete, review, etc.), vous pouvez faire autre chose. Les sons vous alertent quand :

- **finish.mp3** : Claude a termine, vous pouvez voir le resultat
- **need-human.mp3** : Claude a besoin de votre input pour continuer

### Configuration par OS

#### macOS (par defaut)

```json
{
  "command": "afplay /chemin/vers/fichier.mp3 &"
}
```

`afplay` est inclus dans macOS par defaut.

#### Linux

```bash
# Installer un lecteur audio
sudo apt install mpv  # ou ffplay, aplay, paplay

# Configuration
{
  "command": "mpv --no-video /chemin/vers/fichier.mp3 &"
}
```

Alternatives :
- `ffplay -nodisp -autoexit fichier.mp3 &`
- `aplay fichier.wav &` (WAV uniquement)
- `paplay fichier.mp3 &` (PulseAudio)

#### Windows (PowerShell)

```json
{
  "command": "powershell -c (New-Object Media.SoundPlayer 'C:\\chemin\\fichier.wav').PlaySync()"
}
```

Ou avec un lecteur externe :
```json
{
  "command": "start /min wmplayer C:\\chemin\\fichier.mp3"
}
```

### Remplacer les sons

1. Placez vos fichiers `.mp3` dans `.claude/song/`
2. Mettez a jour les chemins dans `settings.json`

**Recommandations :**
- Sons courts (< 5 secondes)
- Volume modere
- Distincts l'un de l'autre

### Desactiver les sons

Supprimez ou commentez les hooks dans `settings.json` :

```json
{
  "hooks": {
  }
}
```

---

## Personnalisation pour Votre Stack

Ce framework est **independant du langage et du framework**. Les patterns sont universels.

### Principe de base

Les agents et commandes fonctionnent avec **n'importe quelle stack** car ils :

1. **Explorent** votre code existant (pas de presuppose sur la structure)
2. **Cherchent des patterns** dans votre projet
3. **S'adaptent** a vos conventions

### Adapter CLAUDE.md

#### Frontend : Next.js → Vue/Svelte/Angular

**Original (Next.js) :**
```markdown
- **Frontend**: Next.js 16 (App Router), TypeScript, TailwindCSS, React Query, Shadcn UI
```

**Vue.js :**
```markdown
- **Frontend**: Vue 3 (Composition API), TypeScript, TailwindCSS, Pinia, PrimeVue
```

**Svelte :**
```markdown
- **Frontend**: SvelteKit 2, TypeScript, TailwindCSS, Svelte stores
```

**Angular :**
```markdown
- **Frontend**: Angular 17, TypeScript, TailwindCSS, NgRx, Angular Material
```

#### Backend : Go → Python/Rust/Node.js

**Original (Go) :**
```markdown
- **Backend**: Go (Golang), Fiber, Clean Architecture
```

**Python :**
```markdown
- **Backend**: Python 3.12, FastAPI, Clean Architecture
```

**Rust :**
```markdown
- **Backend**: Rust, Axum, Clean Architecture
```

**Node.js :**
```markdown
- **Backend**: Node.js, NestJS, Clean Architecture
```

#### Database : Supabase → Autres

**Original (Supabase) :**
```markdown
- **Database**: Supabase (PostgreSQL), Redis
```

**PostgreSQL direct :**
```markdown
- **Database**: PostgreSQL 16, Redis
```

**MySQL :**
```markdown
- **Database**: MySQL 8, Redis
```

**MongoDB :**
```markdown
- **Database**: MongoDB 7, Redis
```

### Adapter les agents

#### explore-db sans Supabase

Si vous n'utilisez pas Supabase, modifiez `.claude/agents/explore-db.md` pour utiliser vos outils :

```markdown
# Pour PostgreSQL direct
Utilise Bash avec `psql` en read-only:
- psql -c "SELECT * FROM information_schema.tables"
- psql -c "SELECT * FROM pg_indexes"

# Pour MongoDB
Utilise Bash avec `mongosh`:
- mongosh --eval "db.getCollectionNames()"
- mongosh --eval "db.users.find().limit(5)"
```

### Patterns universels a conserver

Ces patterns fonctionnent avec **toute stack** :

| Pattern | Description | Adaptable a |
|---------|-------------|-------------|
| **Clean Architecture** | Separation en layers | Tout langage backend |
| **Explore → Plan → Validate → Implement** | Workflow de dev | Tout projet |
| **DRY** | Pas de duplication | Universel |
| **Configuration externe** | Pas de hardcode | Universel |
| **Error handling exhaustif** | Gerer tous les cas | Universel |
| **i18n** | Pas de strings hardcodes | Tout frontend |

### Checklist de personnalisation

- [ ] Mettre a jour la stack dans `CLAUDE.md` (section Project Overview)
- [ ] Adapter les chemins d'exemple (`frontend/src/`, `backend/internal/`)
- [ ] Modifier les commandes de build si differentes (`npm run dev`, `go run`, etc.)
- [ ] Adapter `explore-db.md` si pas Supabase
- [ ] Mettre a jour les chemins audio dans `settings.json`
- [ ] Adapter les conventions de nommage si differentes
- [ ] Ajouter vos propres libraries dans les exemples Context7

---

## Philosophie & Design Patterns

### DRY (Don't Repeat Yourself)

**Avant de creer** quoi que ce soit, Claude est instruit de :

1. Chercher l'existant dans le projet
2. Verifier les patterns etablis
3. Reutiliser et adapter

Cela evite la duplication et maintient la coherence.

### Explore → Plan → Validate → Implement → Verify

Chaque commande suit ce cycle :

```
EXPLORE    → Collecter le contexte (agents en parallele)
     ↓
PLAN       → Analyser et planifier
     ↓
VALIDATE   → Obtenir l'approbation utilisateur
     ↓
IMPLEMENT  → Coder
     ↓
VERIFY     → Tester (build, tests, DB)
```

**Pourquoi ?**
- Pas d'implementation sans contexte complet
- L'utilisateur garde le controle
- Verification systematique

### Ultra Think Strategy

A chaque transition majeure, Claude reflechit :

```
"Ai-je explore tous les aspects pertinents ?"
"Y a-t-il des edge cases que j'ai manques ?"
"Mon plan est-il coherent avec l'existant ?"
```

### Clean Architecture

Separation stricte des responsabilites :

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

Les dependances vont vers l'interieur (Domain ne depend de rien).

### Validation utilisateur obligatoire

Claude **ne code jamais** sans approbation prealable pour les taches non triviales. Cela garantit :

- Alignement avec les attentes
- Pas de surprise
- Opportunite de correction avant implementation

---

## Troubleshooting

### Les sons ne jouent pas

**macOS :**
```bash
# Tester manuellement
afplay .claude/song/finish.mp3

# Verifier les permissions
# System Preferences → Security & Privacy → Privacy → Microphone
```

**Linux :**
```bash
# Installer mpv si pas present
sudo apt install mpv

# Tester
mpv --no-video .claude/song/finish.mp3
```

### Les agents ne sont pas reconnus

Verifiez que :
1. Le dossier `.claude/agents/` existe
2. Les fichiers `.md` ont le bon format YAML frontmatter
3. Vous etes dans le bon repertoire projet

### Les commandes ne fonctionnent pas

```bash
# Verifier la structure
ls -la .claude/commands/

# Les fichiers doivent avoir l'extension .md
# et le bon format YAML frontmatter
```

### Context7 ne trouve pas ma librairie

Fallback vers WebSearch :
```
# Dans votre prompt a Claude
"Utilise WebSearch pour trouver la doc de [librairie]"
```

### Claude ignore mes guidelines

Verifiez que :
1. `CLAUDE.md` est a la racine du projet
2. Le fichier est bien formate (pas d'erreurs de syntaxe markdown)
3. Relancez Claude Code pour recharger les instructions

---

## Contributing

Les contributions sont les bienvenues !

### Comment contribuer

1. **Fork** le repository
2. **Creez** une branche (`git checkout -b feature/ma-feature`)
3. **Committez** vos changements (`git commit -m 'feat: Add ma feature'`)
4. **Push** (`git push origin feature/ma-feature`)
5. **Ouvrez** une Pull Request

### Guidelines

- Suivez les conventions existantes
- Documentez les nouvelles fonctionnalites
- Testez vos changements
- Un PR = une feature/fix

### Idees de contributions

- Nouveaux agents specialises
- Nouvelles commandes de workflow
- Support d'autres bases de donnees
- Traductions
- Ameliorations de documentation

---

## License

MIT License - voir [LICENSE](LICENSE)

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

## Auteur

**[@Aurealibe](https://github.com/Aurealibe)**

---

*Made with Claude Code*
