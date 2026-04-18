# Cost efficiency — architecture for real launches

## Problem

Executing this skill with a frontier model (Opus/Sonnet) burns tokens. Each post = dozens of tool calls (take_snapshot, take_screenshot, click, type_text). Each tool result can be 50k+ characters of HTML/a11y tree. Five subs × 30–40k tokens each = 150–200k tokens of premium-model context just for mechanical form-filling.

## Recommended 3-pass architecture

### 1. Planning pass — expensive model, one-shot

Opus or Sonnet reads the project, drafts the launch brief, writes per-sub titles + bodies, picks target communities. Outputs structured JSON:

```json
{
  "project": "...",
  "repo_url": "...",
  "targets": [
    {"sub": "r/ClaudeAI", "title": "...", "body": "...", "flair": "Built with Claude", "link_policy": "body"},
    {"sub": "r/AI_Agents", "title": "...", "body": "...", "flair": "Discussion", "link_policy": "comment"}
  ]
}
```

One prompt, ~10k tokens. Saved as `launch-plan.json`.

### 2. Execution pass — cheap model, scripted

A companion script (`scripts/launch_runner.py`) drives the browser via the chosen stack below, using a cheaper model for per-step decisions. The script consumes `launch-plan.json` and executes target-by-target.

**Model choice (recommended order):**
- **Claude Haiku 4.5** via `ANTHROPIC_API_KEY` — excellent vision, ~10–15× cheaper than Opus, same SDK. Drop-in swap. Drafts + execution can both use it if quality OK.
- **Gemini 2.5/3 Flash** — cheapest overall, generous free tier via Google AI Studio for users with Advanced sub.
- **GPT-5 Nano / 4.1 Mini** — fine, requires separate OpenAI billing.

**Browser stack for the script:**
- **Playwright + stealth** — `playwright-extra` + `puppeteer-extra-plugin-stealth`, or `rebrowser-playwright`. Real anti-detection patches.
- Use `connectOverCDP('http://127.0.0.1:9222')` into a Chrome started with `--remote-debugging-port=9222 --user-data-dir=/path/to/profile`, to reuse logged-in state.
- Or connect into the gstack-browse Chromium on port 34567 (same stealth stack, already running).

Only escalate back to the expensive model when hitting a truly ambiguous case (unexpected captcha, novel rule text, mod removal notice).

### 3. Error-escalation pass — expensive model, on-demand

The script holds a retry queue. At end of run, if anything is still `FAILED`, package the failure context (screenshot, rule text, URL) and send ONE batched prompt to the expensive model for resolution suggestions.

## When to skip the script

- One-off launches with 2–3 targets.
- Brand-new subs where rules aren't known and live judgment is needed.
- When the skill's behavior is still being tuned.

## When the script is worth it

- Launches targeting 5+ subs.
- Repeated launches (every project the user ships).
- Scheduled tail posts via `/schedule` — the script is what cron can actually invoke.

## Alternative: use gstack-browse's built-in sidebar agent

If gstack-browse is the chosen browser (see `browser-setup.md`), the Side Panel already runs a child Claude instance that executes tasks in the browser. This is the cost-efficiency architecture built-in — the parent session doesn't eat every tool call. For users with gstack, this may beat writing a custom runner script.

## Deliverable (not yet built)

A separate PR can add `scripts/launch_runner.py` + `scripts/plan_launch.py` to the repo. Until that exists, the skill runs directly under Opus/Sonnet — working, but expensive.
