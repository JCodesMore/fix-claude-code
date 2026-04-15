# Core Performance Settings — Detailed Reference

Use this reference when presenting and applying the 8 core fixes. Each section includes: what the setting does, why it helps, the recommended value, caveats, and where it goes in `~/.claude/settings.json`.

---

## 1. CLAUDE_CODE_EFFORT_LEVEL = "max"

**Location:** `env` block in settings.json

**What it does:** Controls how much internal reasoning Claude does before responding. "max" means full reasoning on every single turn — no shortcuts.

**Plain English:** Think of it like asking someone to really think through their answer instead of giving a quick response. "max" means Claude always takes its time.

**Levels available:** `low` → `medium` → `high` → `max`

**Caveat:** "max" uses more tokens per turn, which means higher cost on API plans. On Max subscription plans, this is included. The `effortLevel` field in settings.json only supports up to "high" — to persist "max", it must be set as this environment variable.

**Gotcha:** The prompt trigger "ultrathink" activates "high", NOT "max". If you're already on max, saying "ultrathink" actually *downgrades* that turn.

**Source:** @hqmank on X, Claude Code model config docs

---

## 2. CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING = "1"

**Location:** `env` block in settings.json

**What it does:** Disables the "adaptive reasoning" feature that dynamically allocates thinking tokens based on perceived task difficulty. When disabled, Claude uses a fixed, generous thinking budget controlled by MAX_THINKING_TOKENS.

**Plain English:** Normally Claude decides how hard to think based on what you ask. Sometimes it under-estimates. This makes it think deeply every time, no matter what.

**Why it helps:** Adaptive thinking sometimes under-allocates on tasks that seem simple but aren't. Disabling it prevents those "lazy" responses where Claude barely reasons before answering.

**Source:** @hqmank on X, Anthropic adaptive thinking docs

---

## 3. MAX_THINKING_TOKENS = "31999"

**Location:** `env` block in settings.json

**What it does:** Sets the upper limit on how many tokens Claude can use for internal reasoning (the "thinking" that happens before the visible response). 31,999 is the maximum supported value.

**Plain English:** This is the size of Claude's "scratch pad" for working through problems. Bigger scratch pad = more thorough reasoning.

**Works with:** When adaptive thinking is disabled (#2 above), this value becomes the fixed budget. When adaptive thinking is enabled, this is the ceiling.

**Source:** Claude Code env vars docs, community testing

---

## 4. CLAUDE_CODE_DISABLE_1M_CONTEXT = "1"

**Location:** `env` block in settings.json

**What it does:** Disables the extended 1-million-token context window, keeping Claude on the standard context size. This frees up compute resources that would otherwise go toward managing the larger context.

**Plain English:** Instead of giving Claude a huge filing cabinet (which takes energy to search through), this keeps it focused with a smaller, faster workspace. The compute saved goes toward deeper thinking.

**Trade-off:** You lose the ability to work with very large codebases in a single session. For most projects, the standard context window is sufficient.

**Source:** @hqmank on X

---

## 5. alwaysThinkingEnabled: true

**Location:** Top-level field in settings.json (NOT in the `env` block)

**What it does:** Forces extended thinking mode on every turn, regardless of model defaults. This is a settings.json field, not an environment variable.

**Plain English:** Makes sure Claude always uses its "deep thinking" mode, even for simple questions. Combined with the other settings, this ensures consistently thorough responses.

**Source:** Community testing, settings schema

---

## 6. CLAUDE_CODE_NO_FLICKER = "1"

**Location:** `env` block in settings.json

**What it does:** Forces fullscreen anti-flicker rendering mode. Prevents the screen from flickering during rapid output updates, which can happen on some terminals.

**Plain English:** Makes the screen update smoothly instead of flickering. A small quality-of-life fix.

**Source:** Community env var discovery

---

## 7. CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC = "1"

**Location:** `env` block in settings.json

**What it does:** Disables all non-essential network traffic — telemetry, error reporting, feature flag checks, and other background calls to Anthropic servers. Only the core API calls for your actual conversations remain.

**Plain English:** Stops Claude Code from phoning home in the background. Your data stays local, and there's less network overhead.

**Privacy benefit:** Maximum privacy — no usage telemetry, no error reports, no feature flag polling sent to Anthropic.

**Caveat:** Disabling feature flags may also disable some gated features that rely on server-side configuration. In practice, this rarely matters.

**Source:** Claude Code env vars docs, GitHub issue #45918

---

## 8. CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS = "1"

**Location:** `env` block in settings.json

**What it does:** Enables the agent teams feature, which allows Claude to coordinate multiple sub-agents working in parallel. Unlocks TeamCreate, TaskCreate, TaskUpdate, TaskList, and SendMessage tools.

**Plain English:** Lets Claude split up big tasks and work on multiple things at once using a team of AI agents. Like having a project manager that can delegate to specialists.

**Note:** This is an experimental feature. It works well for research, independent modules, and parallel investigations. It uses significantly more tokens (~7x) than single-agent work.

**Source:** Claude Code agent teams docs

---

## How to Apply

All environment variables go inside the `"env"` block of `~/.claude/settings.json`:

```json
{
  "env": {
    "CLAUDE_CODE_EFFORT_LEVEL": "max",
    "CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING": "1",
    "MAX_THINKING_TOKENS": "31999",
    "CLAUDE_CODE_DISABLE_1M_CONTEXT": "1",
    "CLAUDE_CODE_NO_FLICKER": "1",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1",
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  },
  "alwaysThinkingEnabled": true
}
```

The `alwaysThinkingEnabled` field goes at the top level, NOT inside `env`.

**Merge these into existing settings.** Never overwrite the file — read it first, add/update only the specified keys, and preserve everything else.
