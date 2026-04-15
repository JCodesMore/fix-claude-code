# Session Timer Bump — Rate Limit Optimization Reference

Set up a persistent cloud routine that keeps the rolling 5-hour rate limit window active across all Claude Code sessions. This maximizes how much work can be done before hitting rate limits — and unlike session-only timers, it never expires.

---

## What It Does

Claude Code tracks usage against a rolling 5-hour window for rate limiting. During long work sessions, the window can reset or usage can bunch up, leading to rate limits sooner than expected. The session timer bump creates a cloud routine that pings every 3 hours to keep the rolling window active and spread out — meaning more usable time and throughput before hitting rate limits.

## Why `/schedule` Instead of `CronCreate`

| | `CronCreate` (session cron) | `/schedule` (cloud routine) |
|---|---|---|
| Persistence | Session-only or 7-day max | **Indefinite — never expires** |
| Requires open session | Yes | **No — runs on Anthropic cloud** |
| Survives restarts | Buggy (`durable` flag unreliable) | **Yes — fully cloud-managed** |
| Computer can be off | No | **Yes** |
| Management UI | None | **claude.ai/code/routines** |

## How It Works

Since `/schedule` is a built-in interactive slash command (not a programmable tool), present the command to the user and guide them through setup.

### Step 1: Tell the user to run

```
/schedule
```

### Step 2: Guide them through the interactive prompts

- **Schedule:** Every 3 hours
- **Prompt/task:** `"Session timer bump — keeping the rolling usage window active. No action needed."`

### Step 3: Confirm creation

After the routine is created, the user can verify it via `/schedule list` or by visiting `claude.ai/code/routines`.

## Important Caveats

- **Never auto-expires.** Unlike `CronCreate` recurring tasks (which die after 7 days), `/schedule` routines run indefinitely until the user pauses or deletes them.
- **Runs on Anthropic cloud.** The routine executes on Anthropic's infrastructure — the user's computer doesn't need to be on and Claude Code doesn't need to be open.
- **Daily run limits apply.** Based on plan: Pro = 5 runs/day, Max = 15 runs/day, Team/Enterprise = 25 runs/day. A 3-hour ping uses ~8 runs/day, so Max or higher is ideal.
- **Minimal token cost.** The bump prompt is tiny — it produces a one-line acknowledgment and does not consume meaningful tokens against the rate limit.
- **Requires Pro plan or higher.** The `/schedule` feature requires a Pro, Max, Team, or Enterprise plan with Claude Code on the web enabled.

## Managing the Routine

| Action | How |
|--------|-----|
| List routines | `/schedule list` or visit `claude.ai/code/routines` |
| Update schedule | `/schedule update` or edit in the web UI |
| Run manually | `/schedule run` |
| Pause/delete | Manage from `claude.ai/code/routines` |

## User-Friendly Explanation

When presenting this to the user:

> "Claude Code rate-limits based on a rolling 5-hour window. This sets up a lightweight cloud routine that pings every 3 hours to keep that window rolling smoothly, so you get more usage before being rate-limited. It runs on Anthropic's servers — your computer can be off and it keeps working. It never expires, and you can manage it anytime at claude.ai/code/routines."
