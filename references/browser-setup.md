# Browser setup

The launch is driven by a controllable Chromium. Two supported paths.

## Primary: gstack-browse (preferred)

Why: **built-in anti-bot stealth** (sites like Google and NYTimes work without captchas), **Playwright under the hood**, separate branded Chromium window so the user's real Chrome stays untouched, live activity feed via the Side Panel, and a **child-Claude sidebar agent** that can execute tasks — perfect cost-efficiency architecture.

### Launch

```
/gstack-open-gstack-browser
```

This spins up a visible Chromium on port 34567 with the gstack extension auto-loaded. Everything the agent does is visible and can be monitored via the Side Panel.

### Cookie / login setup (one-time)

gstack-browse has its own profile (`~/.gstack/chromium-profile/`), so Reddit/Twitter/LinkedIn/TikTok logins are NOT inherited from the user's normal Chrome on first use.

Two options to get logged in:

1. **Import cookies from real Chrome** (fastest): run `/gstack-setup-browser-cookies`. Opens an interactive picker — select the domains (reddit.com, twitter.com, linkedin.com, tiktok.com, news.ycombinator.com, indiehackers.com, dev.to, producthunt.com, youtube.com, instagram.com, chatgpt.com, gemini.google.com). After import, logins persist across sessions because it's a Playwright persistent context.

2. **Log in manually once**: navigate to each platform inside the GStack Browser window and log in normally. Cookies persist in the profile.

Preferred: cookie import. Takes 30 seconds and covers everything at once.

### Tab hygiene inside gstack-browse

- Use `$B goto <url>` to navigate the existing tab, or open new tabs via the gstack commands.
- The user's real Chrome is untouched either way — no hijacking concern.

### Why gstack-browse wins for this skill

- **Anti-detection matters more than one-time login friction.** Reddit/LinkedIn/TikTok actively fingerprint CDP. gstack-browse's stealth patches beat chrome-devtools-mcp here.
- **Sidebar agent = cost efficiency.** The Side Panel runs a child Claude instance. The parent session (Opus/Sonnet) doesn't eat every tool call.
- **Visible window + activity feed** — user can watch and intervene.
- **Playwright base** — path of least resistance for the future scripted launch runner (see `cost-efficiency.md`).

## Fallback: chrome-devtools-mcp

Use when gstack is not installed or when the user wants zero-setup same-day launching with existing Chrome logins.

### Launch

```bash
npx chrome-devtools-mcp@latest --autoConnect
```

Register once in `~/.claude.json` under `mcpServers`:

```json
"chrome-devtools-mcp": {
  "command": "npx",
  "args": ["chrome-devtools-mcp@latest", "--autoConnect"],
  "type": "stdio"
}
```

Restart Claude Code; `/mcp` should show it connected.

### Tradeoffs

- **Pro**: `--autoConnect` reuses the user's real Chrome profile → Reddit/Twitter/LinkedIn already logged in, zero setup.
- **Con**: No stealth. CDP is detectable. Higher shadowban risk on strict platforms.
- **Con**: Shares the user's Chrome → tab hijacking is a real failure mode (see below).

### Three official modes (chrome-devtools-mcp README)

1. Default auto-launch — spawns fresh Chrome, no login state. Bad for this skill.
2. `--autoConnect` — attach to running local Chrome via user-data-dir (by `--channel`). **Use this.**
3. `--browser-url http://127.0.0.1:9222` — attach to a Chrome started with `--remote-debugging-port=9222`. Officially supported fallback for sandboxed setups.

Other flags as needed: `--channel`, `--userDataDir`, `--executablePath`, `--headless`, `--isolated`, `--wsEndpoint`, `--wsHeaders`.

Upstream docs: https://github.com/ChromeDevTools/chrome-devtools-mcp — https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/skills/chrome-devtools-cli/SKILL.md

### Tab hygiene (chrome-devtools-mcp only)

Critical when connected to the user's real Chrome. The user is actively working in other tabs (ElevenLabs, ChatGPT, YouTube, dashboards). **Never hijack their tabs.**

- Use `new_page` (not `navigate_page`) for the first URL of the launch.
- For subsequent navigations, reuse the dedicated launch tab with `navigate_page`.
- Never `select_page` onto a user-created tab.
- At start: `list_pages` to confirm the launch tab doesn't already exist.
- At end: `close_page` to clean up. Leaving random Reddit-submit tabs behind is a failure mode.

## Decision rule

- Interactive Claude Code run with 5+ targets → **gstack-browse**.
- One-off quick launch, 2–3 targets, gstack not installed → **chrome-devtools-mcp**.
- Scripted / cron launch runner → **Playwright + stealth plugin + `connectOverCDP`** (see `cost-efficiency.md`).
