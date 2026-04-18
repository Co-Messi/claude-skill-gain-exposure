---
name: gain-exposure
description: Promote a shipped project across many platforms at once — Reddit (many subs, not just one), Twitter/X, Hacker News, IndieHackers, Dev.to, LinkedIn, Product Hunt, Discord/Slack, plus short-form video (TikTok, YouTube Shorts, Instagram Reels). Use when the user asks to "promote X", "gain exposure", "launch X", "build in public", "ship and tell people", "get famous for X", "post about X everywhere", "get this on Reddit / TikTok / Twitter", or when an AI agent is told to announce a release. The skill drives a stealth browser (gstack-browse preferred for anti-detection; chrome-devtools-mcp as fallback when instant logged-in Chrome is needed), reasons visually from screenshots instead of scraping HTML, and dynamically discovers relevant communities rather than following a fixed list. Full video pipeline (Remotion hero sizzle + ElevenLabs TTS + AI clipping tools) and image generation via ChatGPT / Gemini web UI are supported. Detailed guidance lives in the referenced files below.
---

# Gain Exposure

Shipping is half the job. The other half is getting eyes on it. This skill fixes the three ways AI-driven launches fail:

1. **Too narrow on platforms** — one subreddit and stop. Not a launch.
2. **Too narrow on media** — text post, ignoring that short-form video often gets 10–100× the reach.
3. **Bot-detected** — DOM form fills → Reddit/LinkedIn/TikTok flag and shadowban.

## Core rule — cast the widest possible net

**The agent chooses scope. Not a fixed list.** Before posting, look at the project and every platform where its audience lives, then go there. Don't stop at 3 subs if 10 are relevant. Don't skip video if the project has any visual surface.

Minimum coverage for a real launch:
- **Reddit** — as many relevant subs as the agent can identify (dynamically discovered, not fixed)
- **Twitter/X** thread with media
- **Hacker News** Show HN if it qualifies
- **IndieHackers**, **Dev.to / Hashnode / Medium**, **Product Hunt** (if polished), **LinkedIn** story post
- **Discord / Slack** communities user is in
- **Short-form video** — TikTok / YouTube Shorts / Instagram Reels
- **Long-form video** on user's YouTube
- **Newsletters** in the niche

If only one or two platforms have been hit, the task is not done.

## High-level workflow

0. **Intake** — ask project + angle + audience + scope + credentials. See `references/intake-and-preflight.md`.
1. **Launch the browser** — see `references/browser-setup.md` (gstack-browse preferred, chrome-devtools-mcp fallback).
2. **Auth check** — verify logins on every target before touching anything. See `references/intake-and-preflight.md`.
3. **Preflight** — landing-page OG check, UTM registry, Reddit per-sub gate check. See `references/intake-and-preflight.md`.
4. **Draft assets** — per-platform titles + bodies, video pipeline if in scope (`references/video-pipeline.md`), images (`references/image-generation.md`).
5. **Post everywhere** — skip-on-fail, retry-once-at-end, escalate only twice-failed. See "Execution model" below.
6. **Report** — output table with every URL + timestamp + any deferred/skipped.

## Execution model — autonomous with escalation

After intake, run end-to-end. Do NOT stop for confirmation between platforms.

**Main loop:**
1. Work the target list in order.
2. Target fails (auth expired, form bounced, 2FA, captcha, rate limit, upload rejected) → mark `FAILED`, log reason, immediately continue.
3. After all targets attempted once → loop back and retry every `FAILED` once. Fresh run: reload, wait longer, re-screenshot.
4. Still `FAILED` after retry → escalate to user with failure screenshot + suggested next action.

**Post-and-forget.** Job ends when posting is done. Don't monitor engagement or reply to comments — not in scope. Report the URL table and end.

## Detailed references

| Topic | File |
|---|---|
| Browser setup (gstack-browse + chrome-devtools-mcp fallback, cookie import, tab hygiene) | [browser-setup.md](references/browser-setup.md) |
| Step 0 intake + Step 1 auth check + Step 2 preflight (OG / UTM / Reddit sub rules) | [intake-and-preflight.md](references/intake-and-preflight.md) |
| Anti-detection: visual reasoning, timing, blocked handling, ban-avoidance rules | [anti-detection.md](references/anti-detection.md) |
| Image gen via ChatGPT / Gemini web UI | [image-generation.md](references/image-generation.md) |
| Video pipeline (Remotion sizzle + ElevenLabs TTS + clipping tools) | [video-pipeline.md](references/video-pipeline.md) |
| Per-project community targeting + per-platform writing | [community-and-writing.md](references/community-and-writing.md) |
| 18 additional high-leverage tactics | [tactics.md](references/tactics.md) |
| Cost efficiency (Haiku + Playwright runner for 5+ sub launches) | [cost-efficiency.md](references/cost-efficiency.md) |

Read the relevant reference file before executing that phase. Don't try to run the whole skill from this summary alone.

## Output format (end of run)

```
Launch report — <project name>

Reddit:
  - r/<sub1>: <url> — <time>
  - r/<sub2>: <url> — <time>
Twitter thread: <url>
Hacker News: <url>
IndieHackers: <url>
Dev.to: <url>
LinkedIn: <url>
TikTok: <url>
YouTube Shorts: <url>
Instagram Reels: <url>
YouTube long-form: <url>
Discord/Slack posts: <server> — <channel>
Deferred: <platform> (<reason>)
Skipped: <platform> (<reason>)
```
