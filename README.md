# 📋 Codex CLI Cheatsheet

> A comprehensive, up-to-date reference for [OpenAI Codex CLI](https://github.com/openai/codex) — commands, slash commands, AGENTS.md patterns, config, and prompt best practices.

[![Last Updated](https://img.shields.io/badge/updated-2026--08--17-blue)](./codex-cheatsheet.md)
[![Codex CLI](https://img.shields.io/badge/Codex%20CLI-v0.147+-brightgreen)](https://github.com/openai/codex/releases)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](#license)

---

## 📖 What's Inside

→ **[codex-cheatsheet.md](./codex-cheatsheet.md)**

| Section | What you'll find |
|---|---|
| [Installation & Auth](./codex-cheatsheet.md#installation--auth) | Terminal CLI (`curl`/npm/brew), Desktop & Mobile app, VS Code extension, API key + ChatGPT auth |
| [Core CLI Commands](./codex-cheatsheet.md#core-cli-commands-most--least-used) | Tiered reference: daily drivers → cloud → MCP/plugins |
| [Daily CLI Workflows](./codex-cheatsheet.md#daily-cli-workflows-most-useful-patterns) | 8 practical workflows: code review, TDD, refactoring, bug fixes, CI integration, onboarding |
| [Global Flags](./codex-cheatsheet.md#global-flags-pass-to-any-command) | `--sandbox`, `--model`, `--ask-for-approval`, `--yolo`, `--search` |
| [Sandbox Modes](./codex-cheatsheet.md#sandbox-modes---sandbox---s) | When to use `read-only`, `workspace-write`, `writes`, `never` |
| [Approval Modes](./codex-cheatsheet.md#approval-modes---ask-for-approval---a) | Request approval before code execution |
| [Slash Commands](./codex-cheatsheet.md#slash-commands-interactive-mode-most--least-used) | Every `/command` ranked most → least used |
| [Keyboard Shortcuts](./codex-cheatsheet.md#keyboard-shortcuts-tui) | TUI shortcuts: plan mode, cancel, history |
| [AGENTS.md Guide](./codex-cheatsheet.md#agentsmd--project-instructions) | File hierarchy, full template, stack examples |
| [config.toml Reference](./codex-cheatsheet.md#configtoml--key-settings) | All key settings: model, sandbox, features, MCP, network |
| [Models (2026)](./codex-cheatsheet.md#models-2026) | GPT-5.6 Sol/Terra/Luna, o4-mini, o3, Bedrock |
| [Exec / CI Mode](./codex-cheatsheet.md#exec-non-interactive--ci-mode) | Scripted, headless usage; JSON output; audio input |
| [Multi-Agent (V2)](./codex-cheatsheet.md#multi-agent-v2--stable) | Sub-agent config, parallel patterns, token warnings |
| [Agent Development](./codex-cheatsheet.md#agent-development-best-practices-latest-support) | AGENTS.md design, multi-agent coordination, skills, CI integration |
| [Thread Management](./codex-cheatsheet.md#thread-management-v0145) | Naming, pinning, history search, `/import` from Cursor/Claude Code |
| [Agent Plugins](./codex-cheatsheet.md#agent-plugins-v0143) | Install, publish, manifests, Bedrock + Claude marketplaces |
| [Proxy Support](./codex-cheatsheet.md#proxy-support-v0143) | Corporate proxy, custom CA, PAC/WPAD |
| [Prompt Best Practices](./codex-cheatsheet.md#prompt-best-practices) | 4-part structure, plan mode, research-then-implement |
| [Context & Token Management](./codex-cheatsheet.md#context--token-usage) | `/compact`, `/status`, token budgets, reasoning effort, practical commands |
| [Effective Patterns](./codex-cheatsheet.md#effective-patterns) | Skills, hooks, MCP, defensive prompts, iterative refinement |
| [CI/CD Integration](./codex-cheatsheet.md#cicd-integration) | GitHub Actions workflows |
| [Troubleshooting](./codex-cheatsheet.md#troubleshooting) | `codex doctor`, common issues |
| [Quick Reference Card](./codex-cheatsheet.md#quick-reference-card) | One-screen summary of everything |
| [Environment Variables](./codex-cheatsheet.md#environment-variables) | All supported env vars |

---

## ⚡ Quick Start

**Option A — Terminal (CLI)**
```bash
# Install (Mac/Linux)
curl -fsSL https://chatgpt.com/codex/install.sh | sh

# Install (Windows)
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"

# Sign in with your ChatGPT account (Plus/Pro/Teams)
codex login

# Start coding
codex "refactor the auth module to use async/await"
```

**Option B — Desktop / Mobile App**

Download the ChatGPT app for [macOS / Windows](https://chatgpt.com/download) or install from the [App Store](https://apps.apple.com) / [Google Play](https://play.google.com), then open a project and interact with Codex directly in the chat interface.

**Option C — VS Code Extension**
```bash
# Install from the VS Code CLI
code --install-extension openai.codex
```
Or search **"OpenAI Codex"** in the VS Code Extensions panel (`Ctrl+Shift+X` / `Cmd+Shift+X`).

### Most Useful Commands at a Glance

```bash
codex                              # interactive TUI
codex "fix the failing tests"      # TUI with pre-loaded prompt
codex exec "run tests and fix all lint errors" --yolo   # CI / headless
codex resume                       # resume last session
codex -s read-only "explain what this codebase does"    # safe exploration
```

### Key Slash Commands (in TUI)

```
/plan          → gather context before acting (safe!)
/diff          → show all git changes
/review        → PR-style code review
/compact       → compress history to save tokens
/model gpt-5.6-terra → switch model mid-session
/goal "..."    → set persistent objective across sessions
/import        → migrate from Cursor or Claude Code
```

---

## 🗂 AGENTS.md — Quick Template

Drop an `AGENTS.md` at your repo root to give Codex standing instructions for every session:

```markdown
# Project: my-app

## Repo Layout
- `src/api/`     — Express route handlers
- `src/models/`  — TypeScript data models
- `src/tests/`   — Jest test suite

## Build & Test
- Install: `npm ci`
- Test:    `npm test`
- Lint:    `npm run lint`
- Build:   `npm run build`

## Conventions
- TypeScript strict mode; no `any`
- All database access through repository classes only
- Write Jest tests for every new function

## Do NOT
- Modify `migrations/` directly — use `npm run migrate:create`
- Change public REST API shapes without a versioning note
- Hardcode secrets or API keys
```

---

## 🔄 Keeping This Up to Date

This cheatsheet tracks the [openai/codex](https://github.com/openai/codex) releases. To check for updates:

```bash
codex update        # update the CLI
```

Current coverage: **v0.147.0** (2026-08-17)

---

## 📄 License

[MIT](https://opensource.org/licenses/MIT) — free to use, share, and adapt.

---

*Sources: [Codex CLI Reference](https://developers.openai.com/codex/cli/reference) · [AGENTS.md Guide](https://developers.openai.com/codex/guides/agents-md) · [GitHub Releases](https://github.com/openai/codex/releases)*
