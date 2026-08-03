# Codex CLI — Quick Cheatsheet

> OpenAI Codex CLI commands ranked most → least useful, with prompt and context best practices.
> Source: [developers.openai.com/codex](https://developers.openai.com/codex)

---

## Installation & Auth

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

**Examples:**
```bash
# Start interactive session
codex

# Open with prompt pre-loaded
codex "add input validation to the login endpoint"

# Non-interactive (CI / scripts)
codex exec "run tests and fix any failures"
codex exec "refactor auth.py to use dependency injection"

# Attach images (screenshots, designs, diagrams)
codex -i screenshot.png "implement this UI"
codex exec -i design.png "build this component"

# Resume previous session (picker shown)
codex resume
codex resume --last           # skip picker, resume most recent
codex resume <SESSION_ID>     # resume specific session
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
```

### What Costs the Most Tokens

| Action | Cost | Fix |
|---|---|---|
| Reading large files in full | High | Mention specific line ranges |
| Pasting full file contents | High | Reference by path — let Codex read it |
| Long back-and-forth iterations | Medium | `/compact` mid-session |
| Subagents (parallel tasks) | High | Each runs its own context |
| Live web search results | Medium | Use `web_search = "cached"` in config |

### Smart Context Management

```bash
# Before context gets bloated — summarize and compress
/compact

# Give Codex a map instead of letting it explore
codex "Entry point: src/server.ts → routes in src/routes/ → handlers in src/handlers/auth.ts.
Only look at the auth handler."

# Tell Codex what to ignore
codex "Only look at src/auth/ — ignore src/billing/ and src/reporting/"

# Batch related questions in one turn (saves round-trips)
codex "In one pass: (1) where sessions are stored, (2) whether they're encrypted,
(3) what the TTL is. Report before making any changes."
```

### Preserve Context Across Long Tasks

```bash
# Before compacting — anchor the state
codex "Summarize: what we've done, what files changed, what's next"
/compact

# After compact — re-anchor
codex "Continuing the auth refactor. Login and logout updated.
Next: refresh endpoint in src/api/auth.py"

# Use /goal for multi-session continuity
/goal "migrate auth module to async/await — 3/8 files done"
```

### Session Strategy

| Situation | Action |
|---|---|
| New unrelated task | `/new` or start fresh `codex` |
| Same task, long thread | `/compact` with a focus hint |
| Task branches into two directions | `/fork` each branch |
| Research-heavy prep work | Use `-s read-only` to protect state |
| Parallel independent tasks | Use sub-agents via `/agent` |

### Reasoning Effort vs. Token Spend

```toml
# config.toml — set per session or per task type
model_reasoning_effort = "low"     # fast, cheap, simple tasks
model_reasoning_effort = "medium"  # default — balanced
model_reasoning_effort = "high"    # complex bugs, architecture
model_reasoning_effort = "xhigh"   # long multi-file refactors
```

Or override per invocation:
```bash
codex -c model_reasoning_effort=low "rename variable: s/user_id/userId/ in auth.ts"
codex -c model_reasoning_effort=xhigh "debug the memory leak in the event loop"
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
# Create a skill file: ~/.agents/skills/add-tests.md
# Then invoke:
/add-tests
```

### Hooks — Automate Around Codex Actions

```toml
# .codex/config.toml
[[hooks.PostToolUse]]
[[hooks.PostToolUse.hooks]]
command = "npm test -- --passWithNoTests 2>&1 | tail -20"
```

### MCP — Extend Context Beyond the Repo

```bash
# Add GitHub MCP for PR/issue context
codex mcp add github -- npx -y @modelcontextprotocol/server-github

# Add a filesystem MCP for cross-repo access
codex mcp add files -- npx -y @modelcontextprotocol/server-filesystem /path/to/other/repo
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
