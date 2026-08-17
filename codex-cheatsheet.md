# Codex CLI — Quick Cheatsheet

> OpenAI Codex CLI commands ranked most → least useful, with prompt and context best practices.
> Source: [developers.openai.com/codex](https://developers.openai.com/codex) · [GitHub](https://github.com/openai/codex)
> Current version: **v0.147.0** (2026-08-17)

---

## Contents

1. [Installation & Auth](#installation--auth)
   - [Method 1 — Terminal (CLI)](#method-1--terminal-cli)
   - [Method 2 — Desktop & Mobile Application](#method-2--desktop--mobile-application)
   - [Method 3 — VS Code Extension](#method-3--vs-code-extension)
2. [Core CLI Commands](#core-cli-commands-most--least-used)
3. [Daily CLI Workflows](#daily-cli-workflows-most-useful-patterns)
4. [Global Flags](#global-flags-pass-to-any-command)
5. [Sandbox Modes](#sandbox-modes---sandbox---s)
6. [Approval Modes](#approval-modes---ask-for-approval---a)
7. [Slash Commands](#slash-commands-interactive-mode-most--least-used)
8. [Keyboard Shortcuts](#keyboard-shortcuts-tui)
9. [AGENTS.md — Project Instructions](#agentsmd--project-instructions)
10. [config.toml — Key Settings](#configtoml--key-settings)
11. [Models (2026)](#models-2026)
12. [Exec / CI Mode](#exec-non-interactive--ci-mode)
13. [Multi-Agent (V2)](#multi-agent-v2--stable)
14. [Thread Management](#thread-management-v0145)
15. [Agent Development Best Practices](#agent-development-best-practices-latest-support)
16. [Agent Plugins](#agent-plugins-v0143)
17. [Proxy Support](#proxy-support-v0143)
18. [Prompt Best Practices](#prompt-best-practices)
19. [Context & Token Usage](#context--token-usage)
20. [Effective Patterns](#effective-patterns)
21. [CI/CD Integration](#cicd-integration)
22. [Troubleshooting](#troubleshooting)
23. [Quick Reference Card](#quick-reference-card)
24. [Environment Variables](#environment-variables)

---

## Installation & Auth

### Method 1 — Terminal (CLI)

The primary way to use Codex is through the command-line interface (CLI).

```bash
# Recommended: standalone installer (Mac/Linux)
curl -fsSL https://chatgpt.com/codex/install.sh | sh

# Windows (PowerShell)
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"

# Package managers
npm install -g @openai/codex       # npm
brew install --cask codex          # Homebrew (macOS)

# Auth
codex login                        # Sign in with ChatGPT (recommended for Plus/Pro/Teams)
codex login --with-api-key         # paste API key from stdin
codex login --device-auth          # OAuth device code flow
codex login status                 # check auth state
```

**Verify installation:**
```bash
codex --version                    # confirm installed version
codex doctor                       # run diagnostics / check setup
```

**First session:**
```bash
codex                              # open interactive TUI
codex "explain this codebase"      # open TUI with a prompt pre-loaded
```

---

### Method 2 — Desktop & Mobile Application

Codex is built into the **ChatGPT desktop app** (macOS / Windows) and the **ChatGPT mobile app** (iOS / Android). No separate CLI installation is required.

| Platform | How to access |
|---|---|
| **macOS** | Download ChatGPT for Mac from [chatgpt.com/download](https://chatgpt.com/download) → open a chat → type `@Codex` or open a project |
| **Windows** | Download ChatGPT for Windows from [chatgpt.com/download](https://chatgpt.com/download) → same as macOS |
| **iOS** | Install the ChatGPT app from the App Store → tap **Tools** → **Codex** |
| **Android** | Install the ChatGPT app from Google Play → tap **Tools** → **Codex** |

**Requirements:** ChatGPT Plus, Pro, or Teams subscription (or API key for API access).

**Tips for desktop/mobile:**
- Attach screenshots or images to describe a UI you want built.
- Use **voice input** on mobile to describe tasks hands-free.
- Sessions created in the app can be resumed in the terminal with `codex resume <SESSION_ID>`.
- The desktop app provides a side-by-side view: chat on the left, terminal output on the right.

---

### Method 3 — VS Code Extension

The **Codex extension for Visual Studio Code** integrates Codex directly into your editor sidebar, providing context-aware suggestions powered by your open files and workspace.

**Install the extension:**

1. Open VS Code → press `Ctrl+Shift+X` (Windows/Linux) or `Cmd+Shift+X` (macOS) to open the Extensions panel.
2. Search for **"OpenAI Codex"** and click **Install**.
3. Alternatively, install from the marketplace URL:
   ```
   https://marketplace.visualstudio.com/items?itemName=openai.codex
   ```
4. Or install via the VS Code CLI:
   ```bash
   code --install-extension openai.codex
   ```

**Sign in:**
- Open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) → **Codex: Sign In**.
- Choose **Sign in with ChatGPT** (Plus/Pro/Teams) or **Sign in with API Key**.

**Key VS Code commands (Command Palette):**

| Command | Description |
|---|---|
| `Codex: Open Chat` | Open the Codex chat panel in the sidebar |
| `Codex: Edit Selection` | Ask Codex to rewrite the selected code |
| `Codex: Explain Selection` | Get a plain-English explanation of selected code |
| `Codex: Fix Selection` | Ask Codex to fix bugs in selected code |
| `Codex: Generate Tests` | Auto-generate unit tests for selected function/class |
| `Codex: New Session` | Start a fresh Codex session scoped to the workspace |
| `Codex: Resume Session` | Resume the most recent session |
| `Codex: Sign In` | Authenticate with ChatGPT or API key |
| `Codex: Sign Out` | Sign out of the extension |

**Keyboard shortcuts (VS Code):**

| Shortcut | Action |
|---|---|
| `Ctrl+Shift+I` / `Cmd+Shift+I` | Open Codex chat sidebar |
| `Ctrl+K Ctrl+I` / `Cmd+K Cmd+I` | Inline edit selected code |
| `Ctrl+K Ctrl+E` / `Cmd+K Cmd+E` | Explain selected code |

**Extension settings (`settings.json`) — Core:**
```json
{
  "codex.model": "gpt-5.6-terra",
  "codex.sandbox": "workspace-write",
  "codex.autoContext": true,
  "codex.showInlineHints": true,
  "codex.approval": "on-change",
  "codex.reasoning": "medium"
}
```

**Advanced Extension Settings:**
```json
{
  "codex.contextSize": "auto",             // auto|small|large - context window management
  "codex.tokenWarningThreshold": 80,       // warn when tokens reach 80% of limit
  "codex.autoCompact": true,               // auto-compact on token threshold
  "codex.threadNaming": "auto",            // auto|manual - auto-generate thread names
  "codex.codeCompletion": true,            // inline code suggestions
  "codex.diagnostics": true,               // show Codex diagnostic hints
  "codex.formatOnWrite": true,             // format code after Codex edits
  "codex.ignorePatterns": [                // files/folders to exclude from context
    "**/node_modules",
    "**/.git",
    "**/build",
    "**/dist"
  ],
  "codex.skipFiles": [],                   // explicitly skip these file patterns
  "codex.additionalContext": [],           // always include these files/patterns
  "codex.shortcutFocus": "editor"          // editor|chat - focus after applying edits
}
```

**Keyboard shortcuts (VS Code) — Complete:**

| Shortcut | Action | Customizable |
|---|---|---|
| `Ctrl+Shift+I` / `Cmd+Shift+I` | Open Codex chat sidebar | Yes |
| `Ctrl+K Ctrl+I` / `Cmd+K Cmd+I` | Inline edit selected code | Yes |
| `Ctrl+K Ctrl+E` / `Cmd+K Cmd+E` | Explain selected code | Yes |
| `Ctrl+K Ctrl+T` / `Cmd+K Cmd+T` | Generate tests for selection | Yes |
| `Ctrl+K Ctrl+F` / `Cmd+K Cmd+F` | Fix bugs in selection | Yes |
| `Ctrl+K Ctrl+R` / `Cmd+K Cmd+R` | Refactor selection | Yes |
| `Ctrl+K Ctrl+D` / `Cmd+K Cmd+D` | Show diff of last edit | No |
| `Alt+Shift+C` | Clear context & start fresh | Yes |

**Custom keyboard shortcuts (`keybindings.json`):**
```json
[
  {
    "key": "ctrl+alt+e",
    "command": "codex.editSelection",
    "when": "editorTextFocus"
  },
  {
    "key": "ctrl+alt+t",
    "command": "codex.generateTests",
    "when": "editorTextFocus"
  },
  {
    "key": "ctrl+alt+r",
    "command": "codex.refactor",
    "when": "editorTextFocus"
  }
]
```

**Tips for VS Code — Effective Codex Development:**
- **Right-click workflow:** Right-click any selection → **Codex** sub-menu for quick inline actions (edit, explain, fix, test).
- **Auto-context:** The extension automatically passes open files and the `AGENTS.md` at the workspace root as context — no setup needed.
- **Scoped queries:** Use `@workspace` in the chat panel to explicitly scope answers to your project; use `@file` for single-file context.
- **Terminal integration:** Terminal sessions started from VS Code's integrated terminal share the same Codex session as the sidebar.
- **Workspace symbol search:** Use `@symbol:functionName` to reference specific functions across your project in chat.
- **Multi-file edits:** Select multiple files in Explorer, then right-click → **Codex: Edit Multiple** to make coordinated changes.
- **Token visibility:** Enable `"codex.tokenWarningThreshold": 80` in settings to get warned before hitting token limits.
- **Sandbox safety:** Use `"codex.sandbox": "read-only"` for exploration, switch to `"workspace-write"` when ready to edit.
- **Session persistence:** Sessions created in the sidebar are automatically saved and resumable from `Codex: Resume Session` command.

---

## Core CLI Commands (Most → Least Used)

### Tier 1 — Daily Drivers

| Command | What it does |
|---|---|
| `codex` | Launch interactive TUI session |
| `codex "do X"` | Launch TUI with an initial prompt pre-loaded |
| `codex exec "do X"` | Non-interactive, scripted run (CI-safe) |
| `codex resume` | Resume most recent saved session |
| `codex update` | Check for and apply CLI updates |

**Most Useful Daily Examples:**
```bash
# ─── Quick Edits ───
codex "fix typo: s/occured/occurred/ in all .ts files"          # simple text replacements
codex "add error handling to async functions in src/api/"       # targeted scope
codex "convert this to async/await" < function.js              # stdin input

# ─── Code Reviews & Debugging ───
codex "review this for security issues: [paste code]"          # inline code review
codex "explain the memory leak in this: [paste]"               # debugging help
codex -s read-only "what does this codebase do?"               # safe exploration

# ─── Testing & Validation ───
codex "run tests and fix all failures"                         # automated test fix
codex exec "generate unit tests for src/api/users.ts"          # headless test gen
codex "add tests for all edge cases in parseJSON()"            # test coverage

# ─── File-Specific Work ───
codex "refactor src/auth.ts to be more readable"               # single file
codex "convert src/db/schema.sql to TypeScript types"          # cross-type
codex "add JSDoc comments to all exports in src/utils/"        # batch documentation

# ─── Git Integration ───
codex /diff                                    # show git changes in session
codex "commit message: add detailed summary of changes"        # auto-commit message
codex "create PR title and description for our changes"        # PR templates

# ─── Context-Aware Work ───
codex -i screenshot.png "implement this UI exactly"            # visual reference
codex -i wireframe.png "build the components from this design" # wireframe image
codex "using AGENTS.md as reference, fix linting errors"       # use project config

# ─── Sessions & Recovery ───
codex resume                                   # interactive picker
codex resume --last                            # resume most recent (no picker)
codex resume <SESSION_ID>                      # resume specific session by ID
codex fork --last                              # branch into new direction

# ─── Non-Interactive (CI/Scripts) ───
codex exec "run npm test and fix failures"                       # CI mode, requires approval
codex exec --json "list all TODO comments"                       # machine-readable output
codex exec -o result.txt "generate API docs"                     # save to file
```

### Tier 2 — Frequent

| Command | What it does |
|---|---|
| `codex exec resume` | Resume a session non-interactively |
| `codex fork` | Clone current session into a new branch |
| `codex doctor` | Generate diagnostic report for troubleshooting |
| `codex features list` | Show all feature flags and their state |
| `codex features enable <flag>` | Enable a feature flag |
| `codex features disable <flag>` | Disable a feature flag |

**Examples:**
```bash
codex fork                          # fork current session (picker)
codex fork --last                   # fork most recent session
codex doctor                        # diagnose install issues
codex doctor --json                 # machine-readable output
codex features list
codex features enable memories      # turn on persistent memories
codex features enable undo          # turn on undo support
```

### Tier 3 — Project & Cloud

| Command | What it does |
|---|---|
| `codex cloud "do X"` | Run task on Codex Cloud (remote) |
| `codex cloud list` | List cloud tasks |
| `codex apply <TASK_ID>` | Pull Codex Cloud diff into local repo |
| `codex archive <SESSION>` | Archive a session |
| `codex delete <SESSION>` | Permanently delete a session |
| `codex app` | Launch Codex Desktop (macOS/Windows) |
| `codex remote-control pair` | Generate a manual pairing code for a running daemon |

**Examples:**
```bash
codex cloud "fix all type errors" --env MY_ENV_ID
codex cloud list --json
codex apply task_abc123
codex archive my-session
codex delete my-session --force
codex remote-control pair           # pair a remote CLI session
```

### Tier 4 — MCP, Plugins, Sandbox

| Command | What it does |
|---|---|
| `codex mcp add <name>` | Register an MCP server |
| `codex mcp list` | List configured MCP servers |
| `codex mcp remove <name>` | Remove an MCP server |
| `codex mcp login <name>` | Authenticate an MCP server |
| `codex plugin add <name>` | Install a plugin |
| `codex plugin list` | List plugins |
| `codex sandbox <cmd>` | Run a command under Codex sandbox policy |
| `codex completion <shell>` | Generate shell completions |
| `codex mcp-server` | Run Codex as an MCP server (stdio) |

**Examples:**
```bash
# MCP
codex mcp add github -- npx -y @modelcontextprotocol/server-github
codex mcp add my-api --url https://api.example.com/mcp
codex mcp list
codex mcp login github

# Plugins
codex plugin list --available
codex plugin add some-plugin

# Shell completions
codex completion zsh >> ~/.zshrc
codex completion bash >> ~/.bashrc

# Sandbox a command
codex sandbox --permissions-profile :workspace -- npm test
```

---

## Daily CLI Workflows (Most Useful Patterns)

### Workflow 1: Code Review & Quick Fixes

```bash
# Explore the codebase safely
codex -s read-only "explain the authentication flow"

# Review specific file
codex -s read-only "review src/auth/login.ts for security issues"

# Fix issues found
codex "fix the SQL injection vulnerability in src/db/query.ts:45-60"

# Verify the fix
codex "run security audit and confirm the fix works"
```

### Workflow 2: Test-Driven Development

```bash
# Start session
codex

# Generate tests first
codex "generate unit tests for parseUserInput() function"

# Implement feature
codex "implement parseUserInput() to pass all tests"

# Verify
codex "run all tests and show results"

# Compress if needed
/compact
```

### Workflow 3: Multi-File Refactoring

```bash
# Plan the refactor
codex "plan: convert src/api/ to async/await. Files affected? Scope? Risks?"

# Get detailed guidance
/plan  # activate plan mode

# Make changes with checkpoint
codex "convert src/api/routes.ts to async/await"
/thread new "Phase 1: routes.ts complete"

# Continue to next file
codex "convert src/api/handlers.ts to async/await"
/thread new "Phase 2: handlers.ts complete"

# Compress if session is long
/compact "Summary: converted routes.ts and handlers.ts. Next: middleware.ts"

# Final verification
codex "verify all async/await conversions: test suite, linting, type checks"
```

### Workflow 4: Bug Investigation & Fix

```bash
# Start with minimal context
codex -s read-only "where is the memory leak in the event loop?"

# Deep dive
codex "show me the exact line and explain why it leaks memory"

# Use high reasoning
codex -c model_reasoning_effort=high "fix the memory leak: [paste code]"

# Verify fix
codex "verify the fix: run memory profiler and confirm improvement"
```

### Workflow 5: Documentation Generation

```bash
# Generate from code (headless mode)
codex exec --json "list all exported functions in src/ with signatures" > functions.json

# Create docs
codex "using functions.json, generate comprehensive API documentation"

# Save to file
codex exec -o API_DOCS.md "generate README.md with installation, usage, examples"

# Update existing docs
codex "update docs/API.md with new endpoints from src/api/"
```

### Workflow 6: Emergency Bug Fix in Production

```bash
# Fast diagnosis with low reasoning
codex -c model_reasoning_effort=low "error 500 in production: [paste log]. Root cause?"

# Quick fix
codex "implement minimal fix for the 500 error: [paste]"

# Verify
codex exec "run smoke tests against staging"

# Document
codex "create post-mortem entry for this incident"
```

### Workflow 7: Parallel Task Coordination

```bash
# Main session
codex "refactor auth module - [main task description]"

# Spawn sub-agents for independent work
/agent "optimize database queries in audit_log table"
/agent "add unit tests for newly refactored auth functions"
/agent "update API documentation for auth endpoints"

# Check status
/agent status

# Consolidate results
codex "summarize results from all sub-agents and verify no conflicts"
```

### Workflow 8: Large Codebase Onboarding

```bash
# Safe exploration with read-only
codex -s read-only "explain this codebase: directory structure, main entry point, tech stack"

# Deep dive on architecture
codex -s read-only "diagram the architecture: how do API, database, and services interact?"

# Understand conventions
codex -s read-only "what are the coding conventions, patterns, and anti-patterns?"

# Get up to speed
/goal "master this codebase and be ready to implement new features"
```

---

## Global Flags (Pass to Any Command)

### Most Important

| Flag | Values | Description |
|---|---|---|
| `--sandbox, -s` | `read-only` \| `workspace-write` \| `danger-full-access` | Controls what Codex can touch |
| `--ask-for-approval, -a` | `untrusted` \| `on-request` \| `never` | When to pause for human approval |
| `--model, -m` | `gpt-5.5`, `o4-mini`, `o3`, `gpt-4o`, … | Override model for this run |
| `--image, -i` | `path[,path...]` | Attach image(s) to the initial prompt |
| `--search` | boolean | Enable live web search |
| `--profile, -p` | profile name | Layer a named profile on top of base config |
| `-c key=value` | any config key | Override a config value for this invocation |
| `--cd, -C` | path | Set working directory before processing |

### Safety & Automation

| Flag | Description |
|---|---|
| `--dangerously-bypass-approvals-and-sandbox, --yolo` | Skip all approvals and sandboxing (CI/isolated runners only) |
| `--dangerously-bypass-hook-trust` | Run hooks without trust validation |
| `--oss` | Use local open-source provider (requires Ollama) |
| `--no-alt-screen` | Disable alternate screen (TUI piping) |
| `--strict-config` | Error on unrecognized config fields |
| `--enable <feature>` / `--disable <feature>` | Force-enable/disable feature flags |

**Examples:**
```bash
# Use a specific model + sandbox level
codex -m gpt-5.5 -s workspace-write "refactor auth module"

# Non-interactive with full auto (CI use only)
codex exec --yolo "fix lint errors"

# Override config inline
codex -c model_reasoning_effort=high "debug this race condition"
codex -c personality=pragmatic "write tests for order.ts"

# Use fast local model
codex --oss "quick rename: s/getUserById/findUserById/g"

# Enable live web search
codex --search "update dependencies to latest stable versions"
```

---

## Sandbox Modes (--sandbox / -s)

| Mode | File Access | Network | Use When |
|---|---|---|---|
| `read-only` | Read anywhere, no writes | None | Consultative / analysis only |
| `workspace-write` | Read/write inside workspace | Configurable | Default for most tasks |
| `danger-full-access` | Read/write anywhere | Full | Migration scripts, system-wide refactors |

---

## Approval Modes (--ask-for-approval / -a)

| Mode | Behavior | Use When |
|---|---|---|
| `untrusted` | Prompt before every action | Unfamiliar repos, risky operations |
| `writes` | Allow reads silently, prompt only for writes | Read-heavy tasks where writes need review |
| `on-request` | Prompt only when Codex asks | Default interactive use |
| `never` | No prompts, runs automatically | Trusted repos, scripted CI |

---

## Slash Commands (Interactive Mode, Most → Least Used)

### Tier 1 — Every Session

| Command | What it does |
|---|---|
| `/model` | Switch model mid-session |
| `/clear` | Reset terminal, start fresh conversation |
| `/compact` | Summarize earlier turns to save tokens |
| `/status` | Show session config, model, token usage |
| `/diff` | Display Git changes including untracked files |
| `/review` | PR-style code review (no file modifications) |
| `/plan` | Enter plan mode — gather context before acting |

**Examples:**
```
/model gpt-5.5
/model o4-mini
/compact
/status
/diff
/review
/plan
```

### Tier 2 — Frequent

| Command | What it does |
|---|---|
| `/permissions` | Adjust approval requirements on the fly |
| `/new` | Start fresh conversation — optionally name it |
| `/fork` | Clone current conversation into new thread |
| `/fork --temp` | Create a temporary fork (hidden from thread list) |
| `/pin` | Pin the current thread so it stays at the top of the list |
| `/import` | Migrate settings, MCP servers, sessions from Cursor or Claude Code |
| `/goal "objective"` | Set a persistent task objective |
| `/goal pause` / `/goal resume` / `/goal clear` | Manage the active goal |
| `/mention <file>` | Attach a specific file to the conversation |
| `/fast` | Toggle Fast service tier (faster, same model) |

**Examples:**
```
/permissions
/new "auth-refactor"           # start fresh and name the session
/fork
/fork --temp                   # disposable fork for exploration
/pin                           # pin this thread
/import                        # pick Cursor/Claude Code settings to import
/goal "migrate all API handlers to use async/await"
/goal pause
/mention src/api/auth.ts
/fast
```

### Tier 3 — Occasional

| Command | What it does |
|---|---|
| `/agent` | Switch active sub-agent thread (picker) |
| `/side` | Open ephemeral sidebar conversation |
| `/approve` | Retry recently denied auto-review actions |
| `/resume` | Restore a previous saved session |
| `/mcp` | List configured MCP tools |
| `/personality` | Set communication style (friendly/pragmatic/none) |
| `/usage` | View account-level token consumption |

### Tier 4 — Config & Utilities

| Command | What it does |
|---|---|
| `/theme` | Change syntax-highlighting theme |
| `/keymap` | Remap TUI keyboard shortcuts |
| `/vim` | Toggle Vim mode in the composer |
| `/statusline` | Configure footer status items |
| `/title` | Customize terminal window title |
| `/raw` | Toggle raw scrollback mode |
| `/copy` | Copy latest response to clipboard |
| `/feedback` | Submit logs to maintainers |
| `/logout` | Sign out of Codex |
| `/quit` / `/exit` | Close CLI session |

---

## Keyboard Shortcuts (TUI)

| Shortcut | Action |
|---|---|
| `Enter` | Submit message |
| `Shift+Tab` | Enter Plan mode (propose before executing) |
| `Ctrl+C` | Cancel current agent action |
| `Ctrl+L` | Clear terminal screen |
| `Esc` | Cancel / go back |
| `↑ / ↓` | Navigate message history |
| `Tab` | Autocomplete / cycle suggestions |

---

## AGENTS.md — Project Instructions

AGENTS.md is Codex's equivalent of a system prompt for your project. Codex reads it automatically on every session.

### File Hierarchy (Codex reads all, deeper = higher priority)

```
~/.codex/AGENTS.override.md      ← highest priority (personal global overrides)
~/.codex/AGENTS.md               ← personal global defaults
<git-root>/AGENTS.md             ← project-wide rules
<git-root>/.codex/AGENTS.md      ← project config dir (alternative location)
<subdir>/AGENTS.md               ← subdir-specific rules (read when you're in that dir)
```

### AGENTS.md Template

```markdown
# Project: <name>

## Repo Layout
- `src/api/` — FastAPI route handlers
- `src/models/` — SQLAlchemy models
- `src/tests/` — pytest test suite

## Build & Test Commands
- Install: `pip install -e ".[dev]"`
- Test: `pytest -x --tb=short`
- Lint: `ruff check . && mypy src/`
- Format: `ruff format .`

## Engineering Conventions
- Python 3.11+, Pydantic v2 for all schemas
- Parameterised queries only — no string interpolation in SQL
- All endpoints require JWT auth unless explicitly marked public
- Branch: `feature/*` from `main`; PR requires green CI

## Constraints (Do Not)
- Do NOT hardcode secrets or API keys
- Do NOT modify `alembic/versions/` — create new migrations only
- Do NOT change public API contracts without a versioning note

## Definition of Done
1. Feature works as described
2. Unit tests written and passing
3. `ruff` and `mypy` clean
4. No TODO/FIXME left without a linked issue
```

### AGENTS.md Tips
```bash
# Verify Codex is reading your AGENTS.md
codex --ask-for-approval never "Summarize the current instructions."

# Migrate your existing config from Cursor or Claude Code (includes AGENTS.md)
/import

# Limit: 32 KiB combined across all AGENTS.md files
# Use project_doc_max_bytes in config.toml to adjust
```

### AGENTS.md Stack Templates

#### Node.js / TypeScript (Express + Jest)

```markdown
# Project: my-api

## Repo Layout
- `src/routes/`   — Express route handlers
- `src/services/` — Business logic
- `src/models/`   — TypeScript interfaces and Zod schemas
- `src/tests/`    — Jest unit + integration tests

## Build & Test
- Install: `npm ci`
- Test:    `npm test`
- Lint:    `npm run lint`       (eslint + prettier)
- Build:   `npm run build`
- Types:   `npx tsc --noEmit`

## Conventions
- TypeScript strict mode; never use `any` — use `unknown` + type guards
- All DB access through repository classes in `src/repositories/`
- Use Zod for all request/response validation
- Jest for tests; use `describe/it` blocks; mock external services

## Do NOT
- Modify `db/migrations/` manually — run `npm run migrate:new`
- Hardcode secrets — use `process.env` with dotenv-safe
- Change public API response shapes without bumping the version prefix
```

#### Go (standard library + sqlc)

```markdown
# Project: go-service

## Repo Layout
- `cmd/`         — main entrypoints
- `internal/`    — private packages (handlers, services, store)
- `pkg/`         — shared/public packages
- `sqlc/`        — SQL queries (do not hand-edit generated files)
- `migrations/`  — goose migration files

## Build & Test
- Test:   `go test ./...`
- Lint:   `golangci-lint run`
- Build:  `go build ./cmd/...`
- Vet:    `go vet ./...`

## Conventions
- Use `errors.Is` / `errors.As` — never compare error strings
- Context must be the first argument to every function that does I/O
- All exported functions must have a GoDoc comment
- DB queries only via sqlc-generated code in `internal/store/`

## Do NOT
- Edit files under `internal/store/` — regenerate with `sqlc generate`
- Use `panic` in library code
- Add global mutable state
```

#### Java / Spring Boot (Maven)

```markdown
# Project: spring-service

## Repo Layout
- `src/main/java/com/example/` — application code
  - `controller/` — REST controllers
  - `service/`    — business logic
  - `repository/` — Spring Data JPA repositories
  - `dto/`        — request/response DTOs
- `src/test/`     — JUnit 5 tests

## Build & Test
- Test:   `mvn test`
- Build:  `mvn package -DskipTests`
- Lint:   `mvn checkstyle:check`
- Format: `mvn spotless:apply`

## Conventions
- Use constructor injection (never field injection with `@Autowired`)
- All DTOs validated with Bean Validation (`@NotNull`, `@Size`, etc.)
- Service layer must not reference HttpServletRequest
- Integration tests use `@SpringBootTest` with Testcontainers

## Do NOT
- Modify `src/main/resources/db/migration/` — create new Flyway scripts only
- Use `System.out.println` — use SLF4J logger
- Catch and swallow exceptions silently
```

---

## config.toml — Key Settings

Location: `~/.codex/config.toml` (user) or `.codex/config.toml` (project)

```toml
# Model
model = "gpt-5.6-terra"
model_reasoning_effort = "medium"   # minimal | low | medium | high | xhigh
model_verbosity = "medium"          # low | medium | high

# Defaults
personality = "pragmatic"           # none | friendly | pragmatic
sandbox_mode = "workspace-write"    # read-only | workspace-write | danger-full-access
approval_policy = "on-request"      # untrusted | writes | on-request | never

# Web search
web_search = "cached"               # disabled | cached | live

# Features
[features]
memories = true
undo = true
shell_tool = true

# Multi-agent V2
[multi_agent]
enabled = true
max_concurrency = 2
sub_agent_model = "gpt-5.6-sol"
sub_agent_reasoning_effort = "low"

# Custom provider (e.g. Azure OpenAI)
[model_providers.azure]
base_url = "https://YOUR_RESOURCE.openai.azure.com/openai"
env_key = "AZURE_OPENAI_API_KEY"

# MCP server (tool search is now enabled by default)
[mcp_servers.github]
command = "npx"
args = ["-y", "@modelcontextprotocol/server-github"]
env = { GITHUB_TOKEN = "$GITHUB_TOKEN" }

# Network / proxy
[network]
proxy = "http://proxy.corp.example.com:8080"  # optional
ca_bundle = "/etc/ssl/certs/corp-ca.pem"      # optional custom CA

# TUI
[tui]
theme = "monokai"
vim_mode_default = false
```

### Reasoning Effort by Task

| Task | effort setting |
|---|---|
| Quick rename, formatting | `minimal` or `low` |
| Feature implementation | `medium` (default) |
| Debugging, architecture | `high` |
| Complex multi-file refactor | `xhigh` |

---

## Models (2026)

| Model | Speed | Best for |
|---|---|---|
| `gpt-5.6-sol` | Fastest | Quick edits, renames, simple tasks |
| `gpt-5.6-terra` | Fast | Balanced daily coding and debugging |
| `gpt-5.6-luna` | Medium | Complex features, architecture, analysis |
| `gpt-5.5` | Fast | Complex coding, architecture, debugging |
| `gpt-5.1-codex-max` | Medium | Agentic tasks, full SDLC, reasoning-heavy |
| `o4-mini` | Fast | Balanced speed/intelligence (good default) |
| `o3` | Slow | Hardest reasoning problems |
| `gpt-4o` | Fast | Quick tasks, chat-style edits |

> **Amazon Bedrock:** GPT-5.6 Sol, Terra, and Luna are also available via Bedrock for enterprise deployments.

Switch mid-session: `/model gpt-5.6-terra`
Set default in config: `model = "gpt-5.6-terra"`

---

## Exec (Non-Interactive / CI Mode)

`codex exec` is Codex's headless mode — no TUI, no prompts, scriptable.

```bash
# Basic
codex exec "add error handling to all API endpoints"

# With specific model + sandbox
codex exec -m o4-mini -s workspace-write "fix all ruff lint warnings"

# Fully automated (no approvals — CI only)
codex exec --yolo "run tests and fix failures"

# Save output to file
codex exec -o output.txt "generate a test plan for auth module"

# Resume a previous exec session
codex exec resume --last "continue with the remaining endpoints"

# With images
codex exec -i wireframe.png "implement this screen in React"

# JSON output (for scripting)
codex exec --json "list all TODO comments with file and line number"

# Audio input (wav/mp3/ogg/flac)
codex exec -i recording.wav "transcribe and summarize the meeting notes"
```

---

## Multi-Agent (V2 — Stable)

Codex V2 multi-agent is now stable. Sub-agents run as independent parallel threads, each with its own context window.

### Starting Sub-Agents

```
/agent                    # open sub-agent picker / switch threads
/side                     # open a lightweight ephemeral sidebar agent
```

### Configuring Multi-Agent in config.toml

```toml
[multi_agent]
enabled = true
max_concurrency = 4                # max parallel sub-agents (default: 2)
sub_agent_model = "gpt-5.6-sol"   # cheaper/faster model for sub-agents
sub_agent_reasoning_effort = "low" # effort level for sub-agents
```

### Multi-Agent Prompt Patterns

```
# Kick off parallel sub-agents from one turn
"Spawn three agents:
 Agent 1: refactor src/auth/ to async/await
 Agent 2: add unit tests for src/payments/
 Agent 3: update all OpenAPI docs in docs/api/"

# Use /side for quick throwaway research
/side
"What's the fastest regex library for Python 3.12?"
```

> **Warning:** Each sub-agent has its own token context. High concurrency with `Ultra` reasoning can exhaust usage limits quickly. Monitor with `/usage`.

---

## Thread Management (v0.145+)

Codex now supports persistent, named, searchable threads with paginated history.

| Action | How |
|---|---|
| Name a session | `/new "my-session-name"` or `/clear "new-name"` |
| Pin a thread | `/pin` — stays at top of thread list |
| Resume a named thread | `codex resume "my-session-name"` |
| Temporary fork | `/fork --temp` — explore without cluttering thread list |
| Search thread history | `codex resume` then type to search by name/summary |

```bash
# Start a named session
codex "fix the billing module" --session billing-fix

# Resume by name
codex resume "billing-fix"

# Migrate threads and settings from Cursor or Claude Code
codex
/import            # interactive picker: choose what to import
```

---

## Agent Development Best Practices (Latest Support)

### Designing Effective AGENTS.md

Create an `AGENTS.md` file to guide Codex with project-specific knowledge:

```markdown
# Project: my-app (v2.0)

## Repo Layout
- `src/`           — TypeScript source code
- `src/api/`       — Express API routes
- `src/models/`    — TypeScript data models
- `src/tests/`     — Jest test suite
- `docs/`          — API and architecture docs
- `config/`        — Environment and build configs

## Build & Test
- Install: `npm ci`
- Dev:     `npm run dev` (starts server on port 3000)
- Test:    `npm test` (jest, min 80% coverage)
- Lint:    `npm run lint` (eslint + prettier)
- Build:   `npm run build` (typescript → /dist)
- Deploy:  `npm run deploy` (run tests, build, push to prod)

## Conventions
- TypeScript strict mode; no `any` types
- All API responses wrapped in `{ status, data, error }`
- Database access only through repository classes
- All async functions use async/await (no callbacks)
- Jest tests for every function; min 80% coverage
- Comments for WHY, not WHAT
- PR description must include: what changed, why, testing done

## Important Patterns
- Auth via JWT tokens in Authorization header
- All errors logged with context: user_id, request_id, stack
- Database transactions for multi-step operations
- Rate limiting: 100 req/min per user, 1000 req/min per IP

## Do NOT
- Modify `migrations/` directly — use `npm run migrate:create`
- Hardcode secrets, API keys, or environment variables
- Change REST API response shapes without versioning (use `api/v2/` for breaking changes)
- Delete data without audit logging
- Use deprecated dependencies (check `npm audit`)
- 🚫 Deploy on Friday afternoons

## Stack
- Node.js 20+
- Express 5.x
- TypeScript 5.4+
- PostgreSQL 15+
- Jest 29+
```

### Multi-Agent Coordination

**Spawn parallel agents for independent work:**
```bash
codex "Please spawn 3 agents:
Agent 1: Refactor src/auth/ from callback-based to async/await. Update all tests.
Agent 2: Add comprehensive error handling to src/api/error-handler.ts
Agent 3: Update all API documentation in docs/ to match new endpoints"

# Monitor progress
/agent status

# Consolidate results when all complete
codex "Summarize what all agents completed. Check for conflicts between changes."
```

**Use `/side` for lightweight throwaway research:**
```bash
/side
"What's the fastest way to cache database queries in Node.js?"
# (Research agent, doesn't modify code)
```

### Agent Reasoning & Cost Control

```bash
# For agent-driven tasks, control costs by setting reasoning
codex -c model_reasoning_effort=low "/agent refactor-auth"    # simple coordination
codex -c model_reasoning_effort=high "/agent debug-memory"    # complex issues
```

```toml
# Set per sub-agent in config.toml:
[multi_agent]
sub_agent_reasoning_effort = "low"  # agents are fast and cheap
```

### Building Skills for Reuse

**Define a skill (snippet of repeatable code):**
```bash
/skill new "add-error-handling"
"For all async functions in [path]:
 - Add try/catch blocks
 - Log errors with context (file, function, line)
 - Return { status: 'error', message } on failure"

# Reuse skill in future sessions
/skill add-error-handling  # applies to current selection
codex "apply skill: add-error-handling to src/api/routes.ts"
```

### Continuous Integration with Codex Agents

**GitHub Actions workflow** (safe example for protected branches only):
```yaml
name: Codex Automated Fixes
on:
  push:
    branches: [main, develop]  # Restrict to protected branches
jobs:
  codex-fix:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
      - run: |
          # Setup API credentials (use repo secrets)
          export OPENAI_API_KEY="${{ secrets.OPENAI_API_KEY }}"
          
          # Run Codex in CI mode to fix issues (NO --yolo: requires human review)
          # Note: Remove auto-push in production; create PR for review instead
          codex exec "run npm test and fix all failures"
          
          # Verify tests still pass after fixes
          npm test
          
          # Run linting fixes
          codex exec "run npm run lint -- --fix"
          
          # Verify lint passes
          npm run lint
          
          # Option 1: Commit and create PR for review (RECOMMENDED)
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add -A
          if ! git diff --cached --quiet; then
            git commit -m "fix: auto-fixes from Codex"
            git push origin HEAD:codex-auto-fixes-${{ github.run_number }}
            gh pr create \
              --title "Auto-fixes from Codex" \
              --body "Automated fixes from Codex. Please review before merging." \
              --base ${{ github.ref_name }} \
              --head codex-auto-fixes-${{ github.run_number }} \
              || true
          fi
       env:
         GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Security Best Practices:**
- Only trigger on `push` to protected branches (never `pull_request` from forks)
- Never use `--yolo` in automated workflows (requires human review)
- Create PRs for changes instead of auto-pushing
- Use `OPENAI_API_KEY` from repository secrets only
- Test and lint before committing

### Agent Development Checklist

- [ ] Created `AGENTS.md` at repo root with complete project context
- [ ] Set appropriate `model_reasoning_effort` for agent tasks
- [ ] Defined reusable `/skill` templates for common patterns
- [ ] Tested agents in read-only mode first (`-s read-only`)
- [ ] Verified token usage with `/usage` before running expensive tasks
- [ ] Set `max_concurrency` in config.toml to avoid token exhaustion
- [ ] Monitored first few agent runs with `/agent status`
- [ ] Documented custom patterns in `AGENTS.md` for consistency
- [ ] Added post-agent verification steps (tests, linting, type checks)

---

## Agent Plugins (v0.143+)

Plugins extend Codex with packaged skills, tools, and integrations.

```bash
# Browse and install from marketplace
codex plugin list --available
codex plugin add some-plugin

# Publish your own plugin to the workspace
# Define plugin.json manifest → workspace plugin publishing in config.toml

# Supported plugin marketplaces
# - OpenAI (default)
# - Amazon Bedrock
# - Claude Code (cross-compatible)
```

### Plugin Manifest (plugin.json)

```json
{
  "name": "my-linter",
  "version": "1.0.0",
  "description": "Run project linters on every file Codex edits",
  "skills": ["lint-on-save"],
  "tools": []
}
```

```toml
# .codex/config.toml — publish workspace plugin
[plugins.workspace]
manifest = ".codex/plugin.json"
```

---

## Proxy Support (v0.143+)

Codex respects system proxies (macOS, Windows PAC/WPAD) and custom CAs.

```bash
# Set via environment variables
HTTP_PROXY=http://proxy.corp.example.com:8080 codex
HTTPS_PROXY=http://proxy.corp.example.com:8080 codex
NO_PROXY=localhost,127.0.0.1 codex
```

```toml
# config.toml — explicit proxy
[network]
proxy = "http://proxy.corp.example.com:8080"
ca_bundle = "/etc/ssl/certs/corp-ca.pem"
```

---

## Prompt Best Practices

### The 4-Part Structure (Official Recommendation)

```
GOAL:       What outcome do you need?
CONTEXT:    What files/systems/constraints matter?
CONSTRAINT: What must NOT be changed / what rules apply?
DONE WHEN:  How do we know it's complete?

Example:
"GOAL: Add rate limiting to POST /api/login.
 CONTEXT: FastAPI backend in src/api/auth.py; Redis already configured in src/cache.py.
 CONSTRAINT: Do not touch the refresh token logic or any test files.
 DONE WHEN: Tests pass, ruff clean, rate limit returns 429 after 5 attempts/min."
```

### Be Explicit About Scope

```bash
# Vague — Codex may over-read or over-change
codex "fix the login bug"

# Precise — file, line, constraint
codex "Fix JWT expiry check in src/auth/middleware.py:88 — tokens accepted after
expiry. Do not touch refresh logic."
```

### Use Plan Mode for Ambiguous Tasks

```bash
# Shift+Tab in TUI, or:
/plan
# Codex gathers context, asks clarifying questions, proposes a plan
# You approve before any files are touched
```

### Separate Research from Implementation

```bash
# Research first (read-only, cheap)
codex -s read-only "find all places we call getUserById — list file:line only"

# Then implement (after reviewing the list)
codex "update each of those callsites to use getUserByIdOrThrow"
```

### Anchor on the WHY

```bash
# Without context — Codex may pick the wrong fix
codex "remove the setTimeout in checkout.ts"

# With WHY — Codex understands the constraint
codex "Remove the setTimeout in checkout.ts:88 — it was a workaround for a race
condition fixed in commit a3f91b2. The delay now just slows checkout."
```

### Use File References

```bash
codex "the bug is in src/api/orders.ts:134 — status is not validated
against the OrderStatus enum before insert"
```

### Goals for Multi-Session Work

```bash
# Set a persistent objective — survives /compact and /fork
/goal "migrate all API handlers from callbacks to async/await, one file at a time"

# Each new session resumes knowing the mission
/goal resume
```

---

## Context & Token Usage

### Check Usage

```
/status        # tokens used this session + model
/usage         # account-level consumption
/compact       # compress session history (saves tokens, keeps context)
```

### What Costs the Most Tokens

| Action | Cost | Fix |
|---|---|---|
| Reading large files in full | High | Mention specific line ranges: `src/api.ts:50-100` |
| Pasting full file contents | High | Reference by path — let Codex read it |
| Long back-and-forth iterations | Medium | `/compact` mid-session to summarize |
| Subagents (parallel tasks) | High | Each runs its own context; use sparingly |
| Live web search results | Medium | Use `web_search = "cached"` in config |
| Images & attachments | Medium | Compress before attaching; describe visually if possible |
| Reasoning effort (xhigh) | Very High | Use `low`/`medium` for simple tasks, `xhigh` only for complex bugs |

### Smart Context Management — Commands

```bash
# Compress history mid-session (MOST IMPORTANT for long sessions)
/compact

# Give Codex a map instead of letting it explore
codex "Entry point: src/server.ts → routes in src/routes/ → 
       handlers in src/handlers/auth.ts. Only look at the auth handler."

# Tell Codex what to ignore
codex "Only look at src/auth/ — ignore src/billing/ and src/reporting/"

# Batch related questions in one turn (saves round-trips)
codex "In one pass: (1) where sessions are stored, (2) whether they're encrypted,
       (3) what the TTL is. Report findings before making any changes."

# Switch to low reasoning for simple tasks
/model gpt-5.6-sol
codex -c model_reasoning_effort=low "rename variable: s/user_id/userId/"

# Use read-only mode to explore without bloating context
codex -s read-only "explain the auth flow"
```

### Smart Context Management — Patterns

```bash
# Before context gets bloated — summarize and compress
/compact

# Reference files by path (Codex reads on demand)
codex "In src/auth/login.ts and src/auth/logout.ts, 
       add session cleanup. Reference lines 45-60 in login.ts for session handling."

# Use AGENTS.md to set boundaries
# Create AGENTS.md with:
# - Repo Layout (so Codex knows file structure)
# - Do NOT (what to avoid)
# - Conventions (code style)
# Then Codex respects these automatically
```

### Preserve Context Across Long Tasks

```bash
# Before compacting — anchor the state
codex "Summarize: what we've done, what files changed, what's next"
/compact

# After compact — re-anchor with progress
codex "Continuing the auth refactor. Login and logout updated.
Next: refresh endpoint in src/api/auth.py"

# Use /goal for multi-session continuity
/goal "migrate auth module to async/await — 3/8 files done"

# Use threads to organize work
/thread new "Phase 1: Extract auth logic"
/thread new "Phase 2: Add tests"
/thread new "Phase 3: Refactor edge cases"
```

### Session Strategy

| Situation | Command | Token Impact |
|---|---|---|
| New unrelated task | `/new` or start fresh `codex` | Fresh context |
| Same task, long thread | `/compact` with focus hint | Reduces token usage significantly |
| Task branches into two paths | `/fork` to branch session | Each fork independent |
| Research-heavy prep work | `codex -s read-only ...` | Protects state, low approval |
| Parallel independent tasks | `/agent "task 1"` and `/agent "task 2"` | Parallel, each gets own budget |
| Complex multi-file refactor | `codex -c model_reasoning_effort=high ...` | High reasoning with checkpoints |

### Reasoning Effort vs. Token Spend & Speed

```toml
# config.toml — set default per project
model_reasoning_effort = "low"     # fast, cheap, simple tasks (var rename, comments)
model_reasoning_effort = "medium"  # default — balanced (most coding tasks)
model_reasoning_effort = "high"    # complex bugs, architecture decisions
model_reasoning_effort = "xhigh"   # hardest problems: memory leaks, race conditions
```

**Per-invocation override:**
```bash
codex -c model_reasoning_effort=low "rename variable: s/user_id/userId/"
codex -c model_reasoning_effort=xhigh "debug the memory leak in event loop"

codex exec -m gpt-5.6-sol -c model_reasoning_effort=low "simple tasks"
codex exec -m o4-mini -c model_reasoning_effort=medium "moderate tasks"
codex exec -m gpt-5.6-luna -c model_reasoning_effort=high "complex analysis"
```

### Token Budget & Cost Estimation

```bash
# Check before and after
/status                    # before: see current usage

# Estimate for different approaches
codex "Count tokens needed if I: (1) read 5 files, (2) use /compact, (3) run analysis"

# Model token costs (approximate, subject to change)
# Verify current pricing at platform.openai.com/pricing
# gpt-5.6-sol:      ~50% of Terra (fastest, cheapest)
# gpt-5.6-terra:    1x baseline (fast, good for daily work)
# gpt-5.6-luna:     ~2x Terra (slower, more reasoning)
# o4-mini:          ~1.5x Terra (fast with reasoning)
# gpt-5.1-codex-max: ~3x Terra (agentic, full lifecycle)
# o3:               ~5x+ Terra (hardest problems only)
```

### Context Window Settings

```json
// settings.json or codex config
{
  "context_size_limit": "auto",        // auto|64k|128k|200k - adapt to model
  "auto_compact_at_percent": 80,       // auto-compact at 80% full
  "preserve_on_compact": ["goals", "decisions", "summary"],
  "token_warning_threshold": 85        // warn at 85% usage
}
```

---

## Effective Patterns

### Iterative Refinement (Don't Start Over)

```
Turn 1: "Implement password reset flow"
Turn 2: "Good. Add email step — use the mailer in src/mail.py"
Turn 3: "Token should expire in 1h, not 24h — update just that constant"
```

### Defensive Prompts (Prevent Over-Reach)

```
"Do NOT modify any test files."
"Read only — report what you find before making changes."
"Only change the function signature, not the implementation."
"Stop after writing the migration — do not run it."
```

### Skills — Reuse Repeatable Workflows

Skills live in `~/.agents/skills/` or `.agents/skills/` and let you invoke repeatable prompts as slash commands.

```bash
# Create a skill file
mkdir -p ~/.agents/skills
cat > ~/.agents/skills/add-tests.md << 'EOF'
For the file(s) mentioned, write comprehensive unit tests:
1. Cover the happy path, edge cases, and error paths
2. Mock all external dependencies (DB, HTTP, filesystem)
3. Use the same testing framework already in the project
4. Place tests next to the source file or in the nearest `tests/` dir
5. Run the tests to confirm they pass before finishing
EOF

# Invoke in any session:
/add-tests src/services/billing.ts

# More useful skills to create:
# ~/.agents/skills/add-docstrings.md  → add JSDoc/docstrings to a file
# ~/.agents/skills/security-review.md → review a file for security issues
# ~/.agents/skills/pr-description.md  → generate a PR description from /diff
# ~/.agents/skills/explain.md         → explain what a file/function does
```

**Example: pr-description skill**
```markdown
<!-- ~/.agents/skills/pr-description.md -->
Run /diff to see all changes in the current branch, then write a pull request
description with:
- A one-line summary title
- "## What changed" section with bullet points per file group
- "## Why" section explaining the motivation
- "## Testing" section listing how to verify the changes
Keep it concise and technical. Do not modify any files.
```

### Hooks — Automate Around Codex Actions

Hooks run shell commands automatically before or after Codex tool calls.

```toml
# .codex/config.toml

# Run tests after every file write
[[hooks.PostToolUse]]
[[hooks.PostToolUse.hooks]]
command = "npm test -- --passWithNoTests 2>&1 | tail -20"

# Run linter after edits (Python)
[[hooks.PostToolUse]]
match = { tool = "write_file" }
[[hooks.PostToolUse.hooks]]
command = "ruff check . --fix && ruff format . 2>&1 | tail -5"

# Run type-check after every TypeScript write
[[hooks.PostToolUse]]
match = { tool = "write_file", path_glob = "**/*.ts" }
[[hooks.PostToolUse.hooks]]
command = "npx tsc --noEmit 2>&1 | head -30"

# Print a separator before every shell command (visibility)
[[hooks.PreToolUse]]
match = { tool = "shell" }
[[hooks.PreToolUse.hooks]]
command = "echo '──── shell ────────────────────────────────'"

# Run security scan after changes to auth files
[[hooks.PostToolUse]]
match = { tool = "write_file", path_glob = "**/auth/**" }
[[hooks.PostToolUse.hooks]]
command = "npm run audit:auth 2>&1 | tail -10"
```

### MCP — Extend Context Beyond the Repo

```bash
# GitHub — access PRs, issues, review comments
codex mcp add github -- npx -y @modelcontextprotocol/server-github
# Requires: GITHUB_TOKEN env var

# Filesystem — read files outside the current repo
codex mcp add files -- npx -y @modelcontextprotocol/server-filesystem /path/to/other/repo

# PostgreSQL — query your database directly
codex mcp add db -- npx -y @modelcontextprotocol/server-postgres ******localhost/mydb

# Slack — read channels, post messages
codex mcp add slack -- npx -y @modelcontextprotocol/server-slack
# Requires: SLACK_BOT_TOKEN env var

# Brave Search — live web search
codex mcp add search -- npx -y @modelcontextprotocol/server-brave-search
# Requires: BRAVE_API_KEY env var

# List all configured MCP servers and their tools
codex mcp list
/mcp             # list tools from inside a session
```

**MCP in config.toml (persistent)**
```toml
[mcp_servers.github]
command = "npx"
args    = ["-y", "@modelcontextprotocol/server-github"]
env     = { GITHUB_TOKEN = "$GITHUB_TOKEN" }

[mcp_servers.db]
command = "npx"
args    = ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost/mydb"]

[mcp_servers.slack]
command = "npx"
args    = ["-y", "@modelcontextprotocol/server-slack"]
env     = { SLACK_BOT_TOKEN = "$SLACK_BOT_TOKEN" }
```

---

## CI/CD Integration

### GitHub Actions — Automated Codex Tasks

```yaml
# .github/workflows/codex-fix.yml
# Triggered manually or on a label — runs Codex to fix lint errors
name: Codex Auto-Fix

on:
  workflow_dispatch:
    inputs:
      task:
        description: "Task for Codex to perform"
        required: true
        default: "fix all lint errors and failing tests"

jobs:
  codex:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: actions/checkout@v4

      - name: Install Codex CLI
        run: curl -fsSL https://chatgpt.com/codex/install.sh | sh

      - name: Run Codex task
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: |
          codex exec --yolo \
            -m gpt-5.6-terra \
            -s workspace-write \
            "${{ github.event.inputs.task }}"

      - name: Commit and push changes
        run: |
          git config user.name "codex-bot"
          git config user.email "codex-bot@users.noreply.github.com"
          git add -A
          git diff --staged --quiet || git commit -m "chore: codex auto-fix"
          git push
```

```yaml
# .github/workflows/codex-pr-review.yml
# Posts an AI code review on every PR
name: Codex PR Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Install Codex CLI
        run: curl -fsSL https://chatgpt.com/codex/install.sh | sh

      - name: Review PR changes
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: |
          git diff origin/${{ github.base_ref }}...HEAD > /tmp/pr.diff
          codex exec --yolo -s read-only \
            "Review the following diff for bugs, security issues, and missing tests.
             Be concise. Output in markdown with sections: Bugs, Security, Tests.
             $(cat /tmp/pr.diff)"
```

### Useful `codex exec` Scripting Patterns

```bash
# Check if any changes were made (exit 0 = changes, exit 1 = no changes)
codex exec --yolo "fix all TypeScript type errors" && git diff --exit-code

# Pipe Codex output into another tool
codex exec --json "list all TODO comments with file:line" | jq '.[] | .file'

# Loop: run Codex until tests pass (max 3 attempts)
for i in 1 2 3; do
  codex exec --yolo "run tests; fix any failures you find"
  npm test && break
  echo "Attempt $i failed, retrying..."
done

# Generate a changelog entry from recent commits
git log --oneline HEAD~10..HEAD | \
  codex exec --yolo "convert these commit messages into a CHANGELOG.md entry
  following Keep a Changelog format"

# Auto-fix after a failed deployment
codex exec -m gpt-5.6-luna \
  "The deployment just failed. Error log: $(cat deploy.log | tail -50).
   Diagnose the root cause and fix the relevant source files."

# Nightly dependency update task
codex exec --yolo \
  "Update all npm dependencies to their latest minor/patch versions.
   Run the tests. If any break, revert that package and note it."
```

---

## Troubleshooting

### `codex doctor` — Diagnose Issues

```bash
codex doctor                  # interactive diagnostic report
codex doctor --json           # machine-readable (pipe to jq)
codex doctor --json | jq '.checks[] | select(.status == "fail")'
```

### Common Issues

| Symptom | Likely cause | Fix |
|---|---|---|
| `sandbox: permission denied` | Sandbox blocking a required path | Use `-s workspace-write` or add path to sandbox allowlist |
| `context length exceeded` | Too many tokens in session | `/compact` then re-anchor with a summary |
| `rate limit` error | Too many requests | Lower `max_concurrency` in `[multi_agent]`; use `model_reasoning_effort = "low"` |
| MCP server not starting | Missing binary or auth | `codex doctor`; check `codex mcp list`; run `codex mcp login <name>` |
| Wrong model used | Default not set | Set `model = "gpt-5.6-terra"` in `~/.codex/config.toml` |
| Codex touching unrelated files | Prompt too vague | Add explicit file paths and `Do NOT touch X` constraints |
| `update failed` | Network / permissions | `codex update` or reinstall via `curl -fsSL https://chatgpt.com/codex/install.sh \| sh` |

### Reset & Clean State

```bash
# Check what version you're on
codex --version

# Force update to latest
codex update

# Clear a stuck session
/new

# Re-auth if tokens expired
codex login

# Full reinstall
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

---

## Quick Reference Card

```
LAUNCH                          IN SESSION
codex                → TUI      /model     → switch model
codex "prompt"       → TUI+     /plan      → plan before acting
codex exec "prompt"  → headless /compact   → trim tokens
codex resume         → continue /diff      → see changes
codex -i img "..."   → vision   /review    → code review
                                /status    → token usage
FLAGS                           /goal      → set objective
-m gpt-5.6-terra → model        /fork      → branch session
-s ws-write  → sandbox          /fork --temp → temp fork
-a writes    → writes only      /new "name"  → fresh named thread
-a never     → full auto        /pin       → pin thread
--yolo       → bypass all       /import    → migrate from Cursor/Claude
--search     → web search       /agent     → manage sub-agents
-c k=v       → config override  /clear     → reset screen
--oss        → local model
                                SHORTCUTS
CONTEXT TIPS                    Shift+Tab  → plan mode
Mention file:line in prompts    Ctrl+C     → cancel action
/compact before switching tasks Ctrl+L     → clear screen
/goal for multi-session work
-s read-only for research       AGENTS.md
/agent for parallel tasks       ~/.codex/AGENTS.md  → global
                                <root>/AGENTS.md    → project
                                <subdir>/AGENTS.md  → local
                                max 32 KiB total
```

---

## Environment Variables

```bash
OPENAI_API_KEY=sk-...             # OpenAI API key
AZURE_OPENAI_API_KEY=...          # Azure OpenAI key
CODEX_UNSAFE_ALLOW_NO_SANDBOX=1   # disable sandbox warning (not recommended)
HTTP_PROXY=http://proxy:8080      # outbound proxy for auth + API
HTTPS_PROXY=http://proxy:8080     # outbound TLS proxy
NO_PROXY=localhost,127.0.0.1      # bypass list
```

---

*Sources: [Codex CLI Reference](https://developers.openai.com/codex/cli/reference) · [Slash Commands](https://developers.openai.com/codex/cli/slash-commands) · [AGENTS.md Guide](https://developers.openai.com/codex/guides/agents-md) · [Config Reference](https://developers.openai.com/codex/config-reference) · [Best Practices](https://developers.openai.com/codex/learn/best-practices) · [GitHub Releases](https://github.com/openai/codex/releases)*

*Last updated: 2026-08-03*
