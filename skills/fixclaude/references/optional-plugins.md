# Optional Plugin Improvements — Setup Guide

Walk through each tool one at a time. Explain what it does, install it, and confirm success before moving to the next one.

---

## 1. Claude HUD

**What it does:** Adds a persistent status bar at the bottom of your terminal showing context window health (color-coded green/yellow/red), token usage, rate limit tracking, git branch, and active subagents. Think of it like a car dashboard — you can see at a glance how much "fuel" you have left.

**Why it matters:** Without this, you're flying blind on context usage. You won't know you're about to hit the limit until Claude starts degrading.

**Install steps (user must run these manually — they are interactive slash commands):**

Present these commands to the user one at a time using AskUserQuestion, asking them to paste and run each in their Claude Code session:

1. Add the marketplace:
   ```
   /plugin marketplace add jarrodwatts/claude-hud
   ```

2. Install the plugin:
   ```
   /plugin install claude-hud
   ```

3. Run setup:
   ```
   /claude-hud:setup
   ```

**Verification:** After setup, a status line should appear at the bottom of the terminal showing model name, context percentage, and cost.

**Repo:** github.com/jarrodwatts/claude-hud (16,800+ stars)

---

## 2. Context7 MCP Server

**What it does:** Fetches current, version-specific documentation for any library or framework and injects it directly into your prompt. This prevents Claude from using outdated API signatures from its training data.

**Why it matters:** Claude's training data can be months behind. When you're using a library that just released a new version, Context7 ensures you get the right API.

**Install:**

Run this bash command:
```bash
claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
```

**Usage:** Append "use context7" to any prompt that references a library. Example: "How do I set up auth in Next.js 16? use context7"

**Verification:** After adding, run `/mcp` to confirm context7 appears in the server list.

**Repo:** github.com/upstash/context7

---

## 3. Playwright MCP Server

**What it does:** Lets Claude control a real browser (Chromium, Firefox, or WebKit) — navigate pages, click buttons, fill forms, take screenshots, and run automated tests. Claude can verify its own UI changes by actually opening them in a browser.

**Why it matters:** Instead of guessing whether a frontend change works, Claude can test it visually. This dramatically reduces the "it looked right in the code but broke in the browser" problem.

**Install:**

Run this bash command:
```bash
claude mcp add playwright -- npx @playwright/mcp@latest
```

**Usage:** After making frontend changes, ask Claude to "open localhost:3000 and verify the changes" or "take a screenshot of the login page."

**Verification:** After adding, run `/mcp` to confirm playwright appears in the server list.

**Repo:** github.com/microsoft/playwright-mcp

---

## 4. Caveman Skill

**What it does:** A community skill that encourages Claude to produce shorter, more focused output — cutting response tokens by roughly 15-25% while potentially improving accuracy. Based on research showing that constraining LLMs to brief responses improved accuracy by 26 percentage points on some benchmarks.

**Why it matters:** Shorter responses mean less context consumed per turn, which means more turns before you hit the context limit. And the responses may actually be more accurate.

**Install (user must run this manually — it is an interactive command):**

Present this command to the user using AskUserQuestion:
```
/plugin marketplace add juliusbrussee/caveman
/plugin install caveman
```

**Verification:** After installing, ask Claude a coding question and observe whether responses are more concise than usual.

**Repo:** github.com/juliusbrussee/caveman

---

## 5. Claude in Chrome

**What it does:** Anthropic's official Chrome extension that lets Claude Code control your Chrome browser directly from the terminal. Navigate pages, click buttons, fill forms, read console logs, read network requests, and even record GIFs of interactions. It uses your existing browser sessions, so Claude can test authenticated pages.

**Why it matters:** Full-stack development loop — code in the terminal, test in the browser, without switching context. Claude can visually verify its own frontend changes.

**Setup steps:**

1. Check if the extension is already installed by looking for the Chrome extension setting in settings.json.

2. Enable the Chrome integration in Claude Code settings by ensuring the `autoConnectIde` field is set:
   ```json
   {
     "autoConnectIde": true
   }
   ```
   This goes in `~/.claude.json` (note: this is a different file from `~/.claude/settings.json`).

3. Install the Chrome extension from the Chrome Web Store — search for "Claude Code" by Anthropic, or direct the user to install it from their browser.

4. After installing, run `/chrome` in Claude Code to verify the connection.

**Limitation:** Only works in Chrome (not Brave, Arc, or other Chromium browsers running under WSL).

**Docs:** code.claude.com/docs/en/chrome
