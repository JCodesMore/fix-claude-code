# Summary Template — End of Wizard Recap

Use this format to display the final summary after all changes are applied.

---

## Template

```
============================================
   Claude Code Optimization — Complete!
============================================

## Core Performance Fixes Applied

| Setting                                | Before        | After   |
|----------------------------------------|---------------|---------|
| CLAUDE_CODE_EFFORT_LEVEL               | {old or none} | max     |
| CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING  | {old or none} | 1       |
| MAX_THINKING_TOKENS                    | {old or none} | 31999   |
| CLAUDE_CODE_DISABLE_1M_CONTEXT         | {old or none} | 1       |
| alwaysThinkingEnabled                  | {old or none} | true    |
| CLAUDE_CODE_NO_FLICKER                 | {old or none} | 1       |
| CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC | {old or none} | 1     |
| CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS   | {old or none} | 1       |

## Session Timer Bump

{Show "Active — bumps every 3 hours to keep rolling 5-hour rate limit window active (auto-expires in 7 days)" if the user opted in, or "Not set up — skipped" if declined}

## Optional Plugins Installed

{List each installed plugin with a one-line description, or "None — skipped" if the user declined}

- Claude HUD — status dashboard (context %, tokens, rate limits)
- Context7 MCP — live library documentation
- Playwright MCP — browser-based self-testing
- Caveman — concise, focused output
- Claude in Chrome — browser control from terminal

## Quick Tips

- **Restart Claude Code** for all settings to take effect
- Use `/clear` between unrelated tasks to keep context fresh
- Use `/compact` at natural breakpoints to preserve important context
- Watch the HUD status bar — compact when context hits yellow
- Say "use context7" when asking about library APIs
- Use Plan mode (Shift+Tab) before big tasks for better results
- Settings are saved in ~/.claude/settings.json — edit anytime

============================================
   Your Claude Code is now optimized.
   Restart your session to activate.
============================================
```

## Instructions

- Fill in the "Before" column with actual values read from the user's settings (show "not set" if the key was missing)
- Only show plugins in the "Optional Plugins" section if the user chose to install them. Show "None -- skipped" if they declined.
- If a setting was already at the recommended value, show it as "already set -> (unchanged)"
- Only include tool-specific tips for tools that were actually installed (e.g., the HUD tip about "compact when context hits yellow" should only appear if Claude HUD was installed; the Context7 tip should only appear if Context7 was installed)
- Keep the summary clean and scannable — the user should be able to glance at it and know what happened
- Always remind the user to restart Claude Code at the end
