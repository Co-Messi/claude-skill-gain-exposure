# Intake + preflight

## Step 0 — Intake (ASK before you act)

Do NOT guess. Ask in ONE consolidated message.

**Project & angle:**
- Which project? (confirm if obvious from context)
- Strongest angle / headline result (specific number beats vague pitch)?
- Target audience (quant? AI devs? indie builders? generalists?)?
- Single destination URL (landing page / repo / demo / waitlist)?
- Waitlist / "first N users" scarcity angle?

**Tone & scope caps:**
- How aggressive? (quiet soft-launch / medium / full blast)
- Any platforms to SKIP?
- Alt accounts to use (alt Twitter, company LinkedIn, etc.)?

**Credentials & paid tools:**
- **TTS provider?** Default ElevenLabs; alternatives: OpenAI TTS, Play.ht, MiniMax (`/mmx-cli`).
- **Voice API key location** — env var name (`ELEVENLABS_API_KEY`), `.env` path, 1Password, or "paste now". **Never hardcode or commit.**
- **Voice ID** — which voice, clone of user's own voice if available. Dialogue = 2+ voice IDs.
- **Voice params** — stability, similarity, style. Defaults if unspecified: 0.5 / 0.75 / 0.0.
- YouTube channel, public / unlisted / scheduled?
- Clipping tool subscription? (Opus Clip / Submagic / Klap / Vizard / Descript / CapCut)
- Text-to-video for sizzle intro? (Sora / Runway / Pika / MiniMax / skip)
- Music library? (Epidemic / Artlist / Soundstripe / YouTube Audio / skip)
- Image gen provider? (ChatGPT or Gemini via browser, or API fallback)

**Social-proof prime:**
- Who in Discord / Slack / friends to ping for first-hour engagement?

**Outreach:**
- Journalists / newsletter editors to pitch?
- 10k–100k niche accounts to DM a preview?

Summarize answers as a launch brief. Get explicit sign-off on targets and video style before rendering anything.

## Step 1 — Auth check (verify logins BEFORE anything else)

After launching the browser, verify the user is logged into every platform on the target list. Do not assume.

For each target:
1. Navigate to the platform home page via typed address bar.
2. Screenshot.
3. Visually confirm logged-in affordance (avatar / "new post" / username) vs. "Log in" CTA.
4. Report a status table:

```
Reddit       ✓ logged in as u/<username>
Twitter/X    ✓ logged in as @<handle>
Hacker News  ✗ NOT logged in
LinkedIn     ✓ logged in
TikTok       ✗ NOT logged in
YouTube      ✓ logged in as <channel>
IndieHackers ✗ NOT logged in
Dev.to       ✓ logged in
Product Hunt ✗ NOT logged in
```

5. **For any ✗**: STOP and ask the user to log in manually in the attached Chrome. Do not try programmatic login (trips detection; also 2FA/passkeys).
6. If a platform shows logged in but the "create post" page bounces to a login wall → treat as ✗.

Only proceed after every target is ✓.

## Step 2 — Pre-post preflight (three parallel checks)

### 2a. Landing-page OG check

Fetch the target URL and check:
- `<meta property="og:image">` — loads, ~1200×630
- `<meta property="og:title">` and `og:description`
- `<meta name="twitter:card">` (prefer `summary_large_image`)

If anything is missing:
1. Tell the user what's missing.
2. Offer to auto-generate a 1200×630 OG card. **Preferred**: drive ChatGPT or Gemini image mode via the browser (see `image-generation.md`). **Fallback**: `frontend-design` for pixel-perfect typography, or `imagegen` for one-shot.
3. User adds it to the landing page → re-verify → then proceed. Do not post until every major surface has a working preview card.

### 2b. UTM link registry

Generate a markdown table of every URL-to-be-used with UTM tags attached:
`?utm_source=<platform>&utm_medium=<sub-or-channel>&utm_campaign=<launch-slug>`

```
| Platform       | Destination  | UTM-tagged URL |
|----------------|--------------|----------------|
| r/algotrading  | landing page | https://.../?utm_source=reddit&utm_medium=algotrading&utm_campaign=<slug> |
| Twitter thread | landing page | https://.../?utm_source=twitter&utm_medium=thread&utm_campaign=<slug> |
| HN             | landing page | https://.../?utm_source=hn&utm_medium=show&utm_campaign=<slug> |
| TikTok bio     | landing page | https://.../?utm_source=tiktok&utm_medium=bio&utm_campaign=<slug> |
```

Keep the table — the user can reuse it for manual outreach (DMs, newsletters).

### 2c. Reddit per-sub gate check

For every target sub, navigate, screenshot the sidebar/rules, and extract:

- **Required post flair?** Note which to pick.
- **Day-of-week restriction?** (e.g. "Self-promo Saturdays only"). If today is wrong day → mark `DEFER`.
- **Karma / account-age gate?** Cross-check user's karma. If gated → `SKIP`.
- **Self-promotion cap** (e.g. 9:1 rule)? If user's history is thin → flag.
- **No-link rule?** If sub bans outbound links → post as text, drop URL in a comment after.

Preflight table:

```
| Sub                | Flair        | Day OK | Karma OK | Link OK | Status |
|--------------------|--------------|--------|----------|---------|--------|
| r/algotrading      | "Strategy"   | ✓      | ✓        | ✓       | GO     |
| r/quant            | none         | ✓      | ✓        | ✗ text  | GO (text post) |
| r/SideProject      | "Show"       | ✗ Sat  | ✓        | ✓       | DEFER to Saturday |
| r/MachineLearning  | "[Project]"  | ✓      | ✓        | ✓       | GO     |
```

Only `GO` posts proceed in this run. `DEFER` → use `/schedule` to cron for the right day. `SKIP` → drop.
