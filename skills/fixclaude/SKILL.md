---
name: fixclaude
description: This skill should be used when the user asks to "fix claude code", "fix claude", "optimize claude", "improve claude performance", "fix my settings", "setup claude code", "configure claude code", "claude code settings", "tune claude", "fixclaude", "claude is broken", "claude is slow", "claude is lazy", "claude is not working", "make claude better", "keep session alive", "session timer", "rate limit", "avoid rate limits", "rate limited", "fix claude code settings", or wants to optimize their Claude Code installation for maximum performance. Provides an interactive wizard walking through performance settings, rate limit optimization via session timer bump, and optional plugin improvements step by step.
argument-hint: (no arguments needed)
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

### Step 3: Session Timer Bump (Rate Limit Optimizer)

After applying core fixes, offer to set up a persistent scheduled routine for rate limit optimization.

Use **AskUserQuestion** to ask: *"Would you like to set up a session timer bump? Claude Code uses a rolling 5-hour usage window for rate limits. This creates a persistent cloud routine that pings every 3 hours to keep that window rolling, so you get more usage before hitting rate limits. Unlike session-only timers, this runs indefinitely — even when your computer is off. (yes/no)"*

If **no** — skip to Step 4.

If **yes** — load **`references/session-timer.md`** for details. Since `/schedule` is a built-in interactive slash command, present the command to the user and ask them to run it:

Tell the user to paste this command:

```
/schedule
```

Then guide them through the interactive setup:
- **Schedule:** Every 3 hours
- **Prompt/task:** `"Session timer bump — keeping the rolling usage window active. No action needed."`

The `/schedule` command creates a **cloud-hosted routine** managed at `claude.ai/code/routines`. It runs on Anthropic's infrastructure, survives session restarts, works even when the user's computer is off, and **never auto-expires**.

After the user confirms the routine is created, explain:
- It runs every 3 hours on Anthropic's cloud — no need to keep Claude Code open
- They can view and manage it at `claude.ai/code/routines` or via `/schedule list`
- To remove it later: `/schedule list` to find it, then delete from the routines dashboard
- Daily run limits apply based on plan (Pro: 5/day, Max: 15/day, Team/Enterprise: 25/day)

### Step 4: Optional Plugin Improvements

Use **AskUserQuestion** to ask: *"Would you like to explore optional plugin improvements? These add a status dashboard, live docs, browser testing, and more. (yes/no)"*

If **no** — skip to Step 5.

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

### Step 5: Summary

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
- **`references/session-timer.md`** — Rate limit optimization via `/schedule` cloud routine — setup, management, and caveats
- **`references/optional-plugins.md`** — Install instructions and setup guides for each optional tool
- **`references/summary-template.md`** — Format template for the end-of-wizard recap
