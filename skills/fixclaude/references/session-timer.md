# Session Timer Bump — Rate Limit Optimization Reference

Set up an automatic recurring bump that keeps the rolling 5-hour rate limit window active during long Claude Code sessions. This maximizes how much work can be done before hitting rate limits.

---

## What It Does

Claude Code tracks usage against a rolling 5-hour window for rate limiting. During long work sessions, the window can reset or usage can bunch up, leading to rate limits sooner than expected. The session timer bump schedules a lightweight ping every 3 hours that keeps the rolling window active and spread out — meaning more usable time and throughput before hitting rate limits.

## How It Works

Before creating a new timer, check for existing ones using `CronList`. If a session timer bump already exists (from a previous wizard run in this session), skip creation to avoid duplicates.

Use the `CronCreate` tool to schedule a recurring cron job:

```
CronCreate({
  cron: "17 */3 * * *",
  prompt: "Session timer bump — keeping the rolling usage window active. No action needed.",
  recurring: true,
  durable: true
})
```

### Parameters Explained

| Parameter | Value | Why |
|-----------|-------|-----|
| `cron` | `"17 */3 * * *"` | Every 3 hours at :17 past. Offset from :00 to avoid API congestion (best practice from CronCreate docs). |
| `prompt` | `"Session timer bump — keeping the rolling usage window active. No action needed."` | Simple prompt that triggers a minimal response. The goal is to keep the rolling 5-hour window active with regular activity. |
| `recurring` | `true` | Fires on every cron match, not just once. |
| `durable` | `true` | Persists to `.claude/scheduled_tasks.json` — survives session restarts. Each new session picks it up automatically. |

## Important Caveats

- **Auto-expires after 7 days.** Recurring tasks have a maximum lifetime of 7 days, then they auto-delete. Mention this to the user.
- **Only fires while REPL is idle.** If the user is mid-query, the bump waits until they finish. This is fine — an active session is already generating usage.
- **Persists across sessions.** The timer is written to `.claude/scheduled_tasks.json` and resumes automatically when Claude Code restarts. No need to re-run the wizard each session.
- **Minimal token cost.** The bump prompt is tiny — it produces a one-line acknowledgment and does not consume meaningful tokens against the rate limit.

## Verifying the Timer

After creating, use `CronList` to confirm the job is scheduled. The user should see an entry with:
- The cron expression `17 */3 * * *`
- Status: active
- Type: durable (persists across sessions)

## Removing the Timer

If the user wants to cancel the timer mid-session, use `CronDelete` with the job ID returned by `CronCreate`.

## User-Friendly Explanation

When presenting this to the user:

> "Claude Code rate-limits based on a rolling 5-hour window. This sets up a lightweight background ping every 3 hours that keeps that window rolling smoothly, so you get more usage out of long sessions before being rate-limited. It persists across sessions — even if you close and reopen Claude Code, the timer picks back up. It auto-expires after 7 days, so just re-run the wizard to renew it."
