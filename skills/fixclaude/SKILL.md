---
name: fixclaude
description: This skill should be used when the user asks to "fix claude code", "fix claude", "optimize claude", "improve claude performance", "fix my settings", "setup claude code", "configure claude code", "claude code settings", "tune claude", "fixclaude", "claude is broken", "claude is slow", "claude is lazy", "claude is not working", "make claude better", or wants to optimize their Claude Code installation for maximum performance. Provides an interactive wizard walking through performance settings and optional plugin improvements step by step.
argument-hint: (no arguments needed)
allowed-tools: Read, Write, Edit, Bash, AskUserQuestion, Grep, Glob
---

# Fix Claude Code — Interactive Performance Wizard

Run an interactive wizard that detects the user's current Claude Code configuration, applies proven performance fixes, and optionally installs powerful companion tools. Walk through every step in plain language so any user — beginner or expert — understands what's happening and why.

## Tone & Style

Keep explanations short, friendly, and jargon-free. One or two sentences per setting. Use simple analogies when helpful. Never assume the user knows what an environment variable is — just show what changes and why it helps.

## Wizard Flow

### Step 1: Welcome & Detect Current State

Display a brief welcome message explaining what the wizard does.

Read the user's current settings file at `~/.claude/settings.json`. Parse the existing `env` block and top-level fields. Note which of the core fixes are already applied vs missing.

If the file doesn't exist, note that a fresh settings file will be created.

### Step 2: Core Performance Fixes

Load **`references/core-settings.md`** for the detailed explanation of each setting.

Present all 8 core fixes in a clear table showing:
- Setting name
- What it does (plain English, one line)
- Current value → Recommended value

The 8 core fixes are:

| Setting | Purpose |
|---------|---------|
| `CLAUDE_CODE_EFFORT_LEVEL` | Max reasoning every turn |
| `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING` | Consistent deep thinking |
| `MAX_THINKING_TOKENS` | Maximum reasoning budget |
| `CLAUDE_CODE_DISABLE_1M_CONTEXT` | More compute for thinking |
| `alwaysThinkingEnabled` | Extended thinking every turn |
| `CLAUDE_CODE_NO_FLICKER` | Smooth screen rendering |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | Disable telemetry and background network |
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` | Multi-agent coordination |

Note: Mention that `CLAUDE_CODE_EFFORT_LEVEL=max` uses more tokens per turn (higher cost on API plans; included on Max subscriptions). Also note that agent teams (`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS`) uses significantly more tokens when active (~7x) — it enables the capability but does not activate by default, so there is no cost unless the user explicitly uses agent teams.

After presenting the table, use **AskUserQuestion** to confirm: *"Ready to apply these 8 performance fixes? (yes/no)"*

On confirmation, apply settings to `~/.claude/settings.json`. Read the file first, then use the Write tool to write the complete updated JSON (preserving all existing keys). Do NOT use the Edit tool for JSON — it is line-based and can corrupt JSON syntax. Instead, read the full file, merge the new keys in memory, and write the complete result.

After applying, show a short confirmation: what changed.

### Step 3: Optional Plugin Improvements

Use **AskUserQuestion** to ask: *"Would you like to explore optional plugin improvements? These add a status dashboard, live docs, browser testing, and more. (yes/no)"*

If **no** — skip to Step 4.

If **yes** — load **`references/optional-plugins.md`** and walk through each tool one at a time:

1. **Claude HUD** — Visual status bar showing context health, tokens, rate limits
2. **Context7 MCP** — Injects up-to-date library docs into your prompts
3. **Playwright MCP** — Claude can test its own UI changes in a real browser
4. **Caveman skill** — Shorter, more focused output
5. **Claude in Chrome** — Control your browser from the terminal

For each tool:
- Explain what it does in 1-2 plain sentences
- For MCP servers (Context7, Playwright): run the `claude mcp add` bash command directly
- For plugins (Claude HUD, Caveman): present the install command to the user and ask them to run it (these are interactive slash commands that cannot be executed via Bash)
- For Chrome: guide the user through browser extension setup and update `~/.claude.json` (note: this is a *different* file from `~/.claude/settings.json`)
- Confirm success or report any issues

### Step 4: Summary

Load **`references/summary-template.md`** for the recap format.

Display a clear, organized summary of everything that was applied:
- Core settings applied (with before → after values)
- Plugins/tools installed (if any)
- Quick tips for getting the most out of the new setup

End with an encouraging message — the user's Claude Code is now optimized.

## Important Notes

- **Never overwrite existing settings.** Merge changes into the existing file, preserving all hooks, permissions, plugins, and other configuration.
- **Always read before writing.** Read the current settings.json before making any edits.
- **Handle missing files gracefully.** If settings.json doesn't exist, create it with just the new settings.
- **Be honest about trade-offs.** Mention that max effort uses more tokens/costs more. The user should know.
- **Skip what's already done.** If a setting is already at the recommended value, note it as "already set" and move on.

## Reference Files

For detailed information loaded on-demand:
- **`references/core-settings.md`** — Deep explanations, caveats, and sources for each core fix
- **`references/optional-plugins.md`** — Install instructions and setup guides for each optional tool
- **`references/summary-template.md`** — Format template for the end-of-wizard recap
