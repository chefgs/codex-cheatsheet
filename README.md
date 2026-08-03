# 📋 Codex CLI Cheatsheet

> A comprehensive, up-to-date reference for [OpenAI Codex CLI](https://github.com/openai/codex) — commands, slash commands, AGENTS.md patterns, config, and prompt best practices.

[![Last Updated](https://img.shields.io/badge/updated-2026--08--03-blue)](./codex-cheatsheet.md)
[![Codex CLI](https://img.shields.io/badge/Codex%20CLI-v0.146+-brightgreen)](https://github.com/openai/codex/releases)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](#license)

---

## 📖 What's Inside

→ **[codex-cheatsheet.md](./codex-cheatsheet.md)**

| Section | What you'll find |
|---|---|
| [Installation & Auth](#) | `curl` installer, Homebrew, npm, API key + ChatGPT auth |
| [Core CLI Commands](#) | Tiered reference: daily drivers → cloud → MCP/plugins |
| [Global Flags](#) | `--sandbox`, `--model`, `--ask-for-approval`, `--yolo`, `--search` |
| [Sandbox & Approval Modes](#) | When to use `read-only`, `workspace-write`, `writes`, `never` |
| [Slash Commands](#) | Every `/command` ranked most → least used |
| [Keyboard Shortcuts](#) | TUI shortcuts: plan mode, cancel, history |
| [Multi-Agent (V2)](#) | Sub-agent config, parallel patterns, token warnings |
| [Thread Management](#) | Naming, pinning, history search, `/import` from Cursor/Claude Code |
| [Agent Plugins](#) | Install, publish, manifests, Bedrock + Claude marketplaces |
| [Proxy Support](#) | Corporate proxy, custom CA, PAC/WPAD |
| [AGENTS.md Guide](#) | File hierarchy, full template, stack examples |
| [config.toml Reference](#) | All key settings: model, sandbox, features, MCP, network |
| [Models (2026)](#) | GPT-5.6 Sol/Terra/Luna, o4-mini, o3, Bedrock |
| [Exec / CI Mode](#) | Scripted, headless usage; JSON output; audio input |
| [Prompt Best Practices](#) | 4-part structure, plan mode, research-then-implement |
| [Context & Token Management](#) | `/compact`, session strategy, reasoning effort |
| [Effective Patterns](#) | Skills, hooks, MCP, defensive prompts, iterative refinement |
| [CI/CD Integration](#) | GitHub Actions workflows |
| [Troubleshooting](#) | `codex doctor`, common issues |
| [Quick Reference Card](#) | One-screen summary of everything |
| [Environment Variables](#) | All supported env vars |

---

## ⚡ Quick Start

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

Current coverage: **v0.146.0** (2026-07-29)

---

## 📄 License

[MIT](https://opensource.org/licenses/MIT) — free to use, share, and adapt.

---

*Sources: [Codex CLI Reference](https://developers.openai.com/codex/cli/reference) · [AGENTS.md Guide](https://developers.openai.com/codex/guides/agents-md) · [GitHub Releases](https://github.com/openai/codex/releases)*
