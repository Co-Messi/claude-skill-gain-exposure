---
name: gain-exposure
description: Aggressively promote a shipped project across as many platforms as possible — Reddit (pick many relevant subreddits, do NOT stop at one), Twitter/X, Hacker News, IndieHackers, Dev.to, Product Hunt, LinkedIn, Discord/Slack communities, AND short-form video (TikTok, YouTube Shorts, Instagram Reels) generated from demos using AI video-clipping tools (Opus Clip, Submagic, Klap, Vizard, Descript, 2short.ai, Gling, CapCut, etc.) — to maximize public attention after shipping. Use whenever the user has just shipped / launched / finished a project, asks to "promote X", "gain exposure", "get eyes on it", "build in public", "ship and tell people", "get famous for X", "post about X everywhere", "launch X publicly", "get this on TikTok / Twitter / Reddit", or when an AI agent is told to announce a release as part of a larger workflow (e.g. OpenHedge ships → promote everywhere). The skill launches Chrome DevTools MCP via `chrome-devtools-mcp@latest --autoConnect` (preferred for reusing logged-in sessions on Chrome 144+; `--browser-url http://127.0.0.1:9222` is the officially-supported fallback for sandboxed setups) and reasons visually from screenshots instead of scraping HTML, to defeat Reddit/Twitter/LinkedIn bot detection. The agent is expected to DYNAMICALLY discover and pick communities — not be limited to a fixed list — and to cast the widest possible net across text, image, and video platforms.
---

# Gain Exposure

## References (read these if unsure about the MCP)
- Chrome DevTools MCP repo: https://github.com/ChromeDevTools/chrome-devtools-mcp
- Upstream CLI skill (exact launch flags): https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/skills/chrome-devtools-cli/SKILL.md

## Why this skill exists

Shipping a project is half the job — the other half is getting eyes on it. AI agents asked to "promote X" typically fail in three ways, all of which this skill fixes:

1. **Too narrow on platforms.** Agent posts to ONE subreddit and stops. That is not a launch, that is a whimper.
2. **Too narrow on media.** Agent writes a text post and ignores the fact that short-form video (TikTok / Reels / Shorts) gets 10–100x the reach of a Reddit thread when done right.
3. **Bot-detected.** Agent scrapes DOM, fills forms via `document.querySelector`, pastes giant blobs in one tick → Reddit/Twitter/LinkedIn flag the account and shadowban. The post reaches zero humans even though it "succeeded."

## Core rule: no caps, cast the widest possible net

**The agent chooses the scope. Not a fixed list.** Before posting, look at the project, look at every platform where its audience lives, and go there. Do not stop at 3 subreddits if 10 are relevant. Do not skip video if the project has any visual or demo surface.

Minimum coverage for any real launch:

- **Reddit**: as many relevant subreddits as the agent can identify (main, niche, meta/showcase, adjacent). Dynamically discover them — search Reddit, look at where similar projects posted, read sidebars.
- **Twitter/X**: a full thread with images/GIFs, plus reply-tweets tagging relevant accounts/hashtags in the target niche.
- **Hacker News**: Show HN if it qualifies (live URL or repo).
- **IndieHackers**: launch or milestone post.
- **Dev.to / Medium / Hashnode**: technical deep-dive where relevant.
- **Product Hunt**: if polished with a landing page.
- **LinkedIn**: first-person story post.
- **Discord / Slack**: communities the user is already a member of.
- **Short-form video**: TikTok, YouTube Shorts, Instagram Reels — generated from demo footage using AI clipping tools (see Video section).
- **Long-form video**: YouTube upload if footage is worth 2+ minutes.
- **Newsletters / Substack / HN Launch**: if the agent can identify niche newsletters covering this space.

If the agent has only touched one or two platforms, **the task is not done — keep going**. Report back only after the full list has been worked through or consciously skipped with a reason.

## Step 0 — Intake: ask before you act

Before touching the browser, gather scope from Brayden. Do NOT guess. Ask in one consolidated message — not one question at a time.

**Project & angle:**
- Which project is this launch for? (If obvious from context, confirm.)
- What's the strongest angle / headline result? (number, milestone, demo moment)
- Who's the target audience? (quant traders? AI devs? indie builders? generalists?)
- What's the single link you want traffic to go to? (landing page, repo, demo, waitlist)
- Is there a waitlist / "first N users" scarcity framing?

**Tone & scope caps:**
- How aggressive? (quiet soft-launch / medium / full blast across everything)
- Any platforms to SKIP? (some projects aren't a fit for HN, or you don't want LinkedIn noise)
- Any accounts to use besides your default? (alt Twitter, company LinkedIn page, etc.)

**Credentials & paid tools:**
- **Voice (TTS) provider?** — default ElevenLabs; alternatives: OpenAI TTS, Play.ht, MiniMax (via `/mmx-cli` skill). Ask Brayden which.
- **Voice API key — where is it?** Ask Brayden: env var name (e.g. `ELEVENLABS_API_KEY`), `.env` path, 1Password entry, or "I'll paste it now." Do NOT hardcode the key into any file or commit it. If stored in shell env, confirm the agent's subshell can see it (`echo $ELEVENLABS_API_KEY | head -c 8` style check).
- **Which voice?** — ask for voice ID (ElevenLabs) or voice name (OpenAI). Ask if he wants his own cloned voice if he's made one. For dialogue scenes, ask for 2+ voice IDs.
- **Voice parameters?** — stability, similarity, style (ElevenLabs); voice + speed (OpenAI). If Brayden doesn't care, default: stability 0.5, similarity 0.75, style 0.0.
- Which YouTube channel? Public / unlisted / scheduled?
- Which clipping tool subscription is active? (Opus Clip / Submagic / Klap / Vizard / Descript / CapCut)
- Which text-to-video for sizzle intro? (Sora / Runway / Pika / MiniMax / skip)
- Which music library? (Epidemic / Artlist / Soundstripe / YouTube Audio / skip)

**Social-proof prime:**
- Who in your Discord / Slack / friend network should get an early heads-up so they can engage in the first hour?

**Outreach:**
- Any journalists or newsletter editors to pitch directly?
- Any mid-tier niche accounts (10k–100k followers) to DM a preview to?

Summarize the answers back as a launch brief before starting work. Get explicit sign-off on the list of targets and the video style before rendering anything.

## Execution model: autonomous with escalation

After Step 0 intake, Brayden wants the skill to run **autonomously end-to-end**. Do not stop for confirmation between platforms during the main run.

**Main loop:**
1. Work through the target list in order.
2. If a target fails (auth expired mid-run, form bounced, 2FA prompt, captcha, rate limit, upload rejected, rendering error), do NOT block — **mark the target `FAILED` with a short reason, log the failure, and immediately continue to the next target.**
3. After all targets have been attempted once, **loop back** to every `FAILED` target and retry once. Give it a fresh run (reload, wait longer, re-screenshot).
4. Anything still `FAILED` after the second attempt → escalate to Brayden with: what failed, the screenshot of the failure, and suggested next action.

**Post-and-forget**: the job ends when posting is done. Do NOT stick around to reply to comments or monitor engagement — that's not in scope. Report the URL table and end.

## Step 1 — Auth check: verify logins BEFORE anything else

After launching Chrome DevTools MCP with `--autoConnect`, the FIRST thing to do is verify Brayden is logged into every platform on the target list. Do this for every single target; do not assume.

For each target:
1. Navigate to the platform's home page via typed address bar (reddit.com, twitter.com, news.ycombinator.com, linkedin.com, tiktok.com, etc.).
2. Screenshot the page.
3. Visually confirm: is there a username / avatar / "new post" affordance that only appears when logged in? Or is there a prominent "Log in" / "Sign up" CTA?
4. Report status as a table back to Brayden:
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
5. **For any `✗`: STOP and ask Brayden to log in manually in the attached Chrome, then re-run the auth check.** Do not try to log in programmatically — that's exactly the kind of thing that trips bot detection and also Brayden may have 2FA / passkeys / saved browser auth that you don't have credentials for.
6. If a platform shows as logged in but navigating to the "create post" form bounces back to a login wall, treat it as `✗` — sessions can expire mid-flow.

Only proceed to posting after every target on the agreed list is confirmed `✓`.

## Chrome DevTools MCP: launch it correctly

The official README documents three connection modes. For this skill, **prefer `--autoConnect`** — it reuses Brayden's existing Chrome user-data-dir, which means already-logged-in Reddit/Twitter/LinkedIn/TikTok sessions are available without re-auth or captchas.

```bash
npx chrome-devtools-mcp@latest --autoConnect
```

(Also accepted as `--auto-connect`. Requires Chrome 144+.)

### The three documented modes

1. **Default auto-launch** — no flag; the server spawns a fresh Chrome. Bad for this skill because it has no login state.
2. **`--autoConnect`** — attach to a running local Chrome via its user-data-dir (by `--channel`). **Use this** when you need Brayden's logged-in sessions.
3. **`--browser-url http://127.0.0.1:9222`** — attach to a Chrome already started with `--remote-debugging-port=9222`. Also officially supported — not deprecated — and useful in sandboxed/remote environments or when you need to pre-configure Chrome flags yourself. If `--autoConnect` isn't available (older Chrome) or Brayden asks for it, this is a valid fallback, not a bug.

Other documented flags (use as needed): `--channel`, `--userDataDir`, `--executablePath`, `--headless`, `--isolated`, `--wsEndpoint`, `--wsHeaders`.

If any flag is unclear, read the upstream skill: https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/skills/chrome-devtools-cli/SKILL.md

## Anti-detection: reason from screenshots, not HTML

Reddit, Twitter/X, LinkedIn, Product Hunt, TikTok, and Instagram all fingerprint automation. Common tells:

- WebDriver / CDP flags in `navigator`
- DOM-level form fills (`input.value = "..."` or paste in one tick)
- Zero mouse movement, instant submit
- Headless Chrome signatures
- Canvas/WebGL fingerprint anomalies

**The fix is to behave like a human using their eyes.**

### Visual-reasoning workflow

1. **Screenshot** the current page (viewport or full page).
2. **Reason from the image**: "Create Post button is near top-right at roughly (x=1240, y=80). Title field is the large input in the center. Body editor is below."
3. **Act via real input events**: click coordinates, type characters one at a time with human-ish cadence (50–150ms per keystroke with jitter), scroll smoothly.
4. **Screenshot again** to verify state before the next action.
5. **Never** use `document.querySelector(...).value = "..."` to fill forms.
6. **Never** paste a giant text block in one keystroke — type it.

This replicates what a human actually does: look, click, type, look again. Detection systems hunt for DOM manipulation and millisecond-perfect behavior; visual-reasoning-driven input defeats most of them.

### Timing heuristics (tune, don't ignore)

- Page load → first click: **2–5s** (simulate reading)
- Between keystrokes: **50–150ms** with jitter (not fixed)
- Between actions (click → type → click): **500ms–2s**
- Before hitting Submit: re-read the post, pause **3–10s**
- Between posts on different subreddits: **5–15 minutes** — do not rapid-fire
- Between uploads on the same video platform: **10–30 minutes**

## Community targeting: discover, don't follow a fixed list

Pull project context from `/Users/siewbrayden/Desktop/Obsidian/wiki/` (see Brayden's CLAUDE.md). Then **actively search** for where this project's audience lives:

- Search Reddit for prior posts about similar projects → note which subs they landed in → target those subs plus adjacent ones.
- Search Twitter/X for accounts and hashtags discussing this niche → identify the 5–20 most relevant accounts/tags.
- Use web search for "best communities for [topic]", "newsletters covering [topic]", "Discord servers for [topic]".
- Check HN past Show HN posts in the niche — see which ones hit front page and why.

The sample mappings below are **starting points, not limits**. Expand aggressively.

**Trading / quant / fintech** (OpenHedge, PolyAlpha, HyperData):
- Reddit starting points: r/algotrading, r/quant, r/options, r/Daytrading, r/CryptoCurrency (if crypto), r/wallstreetbets (meme angle only), r/SideProject, r/Forex
- HN Show HN, IndieHackers
- Twitter: FinTwit, #quant #algotrading #buildinpublic
- Discord: QuantConnect + various trading servers
- Video: TikTok FinTok, YouTube Shorts finance niche, IG Reels

**AI agents / dev tools** (OpenClaw, Hermes, MiroFish):
- Reddit starting points: r/LocalLLaMA, r/ChatGPT, r/OpenAI, r/singularity, r/MachineLearning (strict rules), r/programming, r/commandline, r/SideProject
- HN Show HN, Dev.to, Product Hunt
- Twitter: #buildinpublic, AI Twitter
- Video: TikTok AI/tech, YouTube Shorts, IG Reels dev-tools niche

**Benchmarks / research** (TrustBench):
- Reddit starting points: r/MachineLearning, r/LocalLLaMA, r/singularity
- HN, arXiv cross-post if paper exists, ML-researcher Twitter threads
- Video: explainer Shorts on YouTube

**Data / analytics terminals** (HyperData):
- Reddit starting points: r/dataisbeautiful, r/datascience, r/algotrading, r/CryptoCurrency
- HN, Dev.to
- Video: data-viz Reels/TikToks

**Design / brand / visual** (website-design, OpenHedge site):
- Reddit starting points: r/webdev, r/Frontend, r/web_design
- Twitter design community, Dribbble, Behance
- Video: design-process TikTok, YouTube Shorts

Always expand past the starting points by actively searching.

## Video: full production pipeline (this is the hero lane)

Short-form video usually out-reaches any text post by 10–100x. **Do not skip this lane.** Budget real time for it — a proper launch video pass is closer to **an hour** than ten minutes.

Every launch should produce at least:
- **One "hero" sizzle video** — 30–60s, futuristic / cinematic, programmatically composed. For Twitter hero tweet, LinkedIn, YouTube Shorts, and the TikTok lead clip.
- **Several short clips** — 15–45s each, auto-cut from demo footage, captioned, for TikTok / Reels / Shorts flooding.
- **One long-form demo** — full walkthrough for YouTube long + as the source feed for short clips.

### Credentials & tools — ASK, don't hardcode

Brayden has multiple API keys and tool subscriptions. **Before picking a tool, ask him which one to use and which credentials to load**, rather than assuming. Ask specifically about:

- **ElevenLabs** — he has API keys; confirm which voice/model he wants before generating voiceover.
- **YouTube channel** — he has his own; ask for channel name + whether to upload as public/unlisted/scheduled.
- **Sora / Runway / Pika / MiniMax / Veo** — ask which text-to-video he prefers for this launch.
- **Submagic / Opus Clip / Klap / Vizard / Descript / CapCut** — ask which clipping tool he's currently subscribed to.
- **Music license** (Epidemic Sound, Artlist, Soundstripe, YouTube Audio Library) — ask which source.

Phrase it: "For this launch I'll need: voiceover, music, and a cinematic intro. You have ElevenLabs keys — which voice? Do you want the intro generated with Sora, Runway, or something else? Which music library?"

### The three tracks (do all of them)

#### Track 1 — Hero sizzle (futuristic, branded)

**Use Remotion** for this track — it gives programmatic, reproducible, branded video, and Brayden already has the `remotion` / `remotion-video-creation` skills available. Invoke those skills for the actual build.

Aesthetic requirements (per Brayden's stated preference — no AI-slop, no gradients):
- Cinematic, futuristic, clean
- Typography-driven hero frames with the project name + one-line promise
- Real screen capture composited into frame (laptop/phone mockups, parallax)
- Motion: subtle, high-end — easing curves, not default spring bounces
- Color: pull from the project's brand if it has one (OpenHedge, HyperData, etc. have brand specs in Obsidian wiki). If no brand exists, ask.
- Duration: 30–60s. Under 30s loses demo room; over 60s loses TikTok.

Audio layer:
- **Voiceover** via the TTS provider chosen in Step 0 (default ElevenLabs). Use the API key location Brayden gave you — never hardcode. Use the voice ID he specified (check if he has a cloned voice of himself — that's usually the best fit for "build in public" content). Script: 80–120 words max for 45s. Render, play it back to him for approval before cutting it into the sizzle.
- **Music bed** — low-key cinematic synth or tech-modern, ducked under VO. Ask Brayden for the library / license source.
- **SFX** — subtle whooshes on major title cards, light UI click SFX on demo segments.
- **Mix** — VO at −3dB, music at −18dB under VO then back up to −10dB in gaps.

#### Track 2 — Short clips (flood)

Feed the long demo recording into a clipping tool (Opus Clip / Submagic / Klap / Vizard — ask Brayden which). Auto-cut 4–8 clips, 15–45s each, 9:16, captions burned in. Rewrite each caption per platform.

#### Track 3 — Long-form demo (YouTube)

Full 3–10 min walkthrough on Brayden's own YouTube channel. Title + thumbnail matter — ask Brayden to approve both before upload. Add timestamps in the description. The short clips and hero sizzle both link back to this long video.

### If no raw footage exists yet

Generate it before clipping:
1. Screen-record a clean demo run (QuickTime on macOS, or Brayden's preferred recorder).
2. Optionally pair with webcam PiP (for IndieHackers/LinkedIn credibility).
3. For the cinematic intro sizzle, generate plates via text-to-video (Sora / Runway / Pika / MiniMax) — **ask Brayden which** — and composite inside the Remotion scene.

### Upload targets for video

- **TikTok** — primary short-form discovery. 9:16, captions burned.
- **YouTube Shorts** — 9:16, same asset; link to long video in description.
- **Instagram Reels** — 9:16, same asset.
- **Twitter/X** — hero sizzle at 1:1 or 16:9 in the launch thread's lead tweet.
- **LinkedIn** — hero sizzle at 1:1 with story caption.
- **YouTube long-form** — the full walkthrough.

Rewrite the caption per platform — never copy-paste. TikTok = punchy + trend-aware. LinkedIn = story-first. YouTube = SEO-aware (project name + what it does + keywords).

### Timing expectation

Plan for ~**60 minutes of real work** for the video lane alone:
- ~15min: script + voiceover generation (ElevenLabs)
- ~20min: Remotion hero scene build/render
- ~10min: auto-clip the demo into shorts
- ~10min: upload long video + thumbnail
- ~5min: upload short clips with per-platform captions

If the agent is producing cheap/"AI-slop" video, stop and ask Brayden to approve the style before continuing — bad video actively hurts the launch.

## Writing the post (per platform, text-side)

**Reddit** — title is 90% of it. Specific number/result beats vague pitch. Body: problem → what you built → link → ask for feedback. **Read the subreddit sidebar** before posting, especially r/MachineLearning, r/algotrading, r/programming. Rewrite each post for the sub's culture — do not copy-paste.

**Twitter/X** — thread, not single tweet. Hook tweet = strongest result or best visual (or video). Tweets 2–6 = problem/build/demo GIF. Final tweet = link + CTA. Visuals on every major tweet.

**Hacker News** — title: `Show HN: [what it does] — [one-line differentiator]`. No marketing language. Link to live demo or repo. One shot per project.

**IndieHackers** — transparency wins. Revenue/user numbers if any, lessons learned, what's next.

**Dev.to / Hashnode / Medium** — technical deep-dive, code snippets, architecture diagrams.

**Product Hunt** — only if polished, has a landing page, and you can rally initial upvotes. One shot per product.

**LinkedIn** — first-person story. "I built X because Y." 3–5 short paragraphs + visual or clip.

**TikTok / Reels / Shorts** — hook in first 1.5s. Captions burned in. Trend-aware sound if applicable.

## End-to-end workflow

0. **Intake** (see Step 0 above): ask Brayden the scope questions and get sign-off on the launch brief. Do not skip.
1. **Gather context**: read the wiki page for the project at `/Users/siewbrayden/Desktop/Obsidian/wiki/`, the README, features, metrics, demo URL/GIF/video.
2. **Discover communities**: actively search Reddit/Twitter/HN/newsletters for where this project's audience lives. Build a target list — don't cap it. Confirm the list with Brayden before posting.
3. **Draft text assets**: 3–5 title variants, per-platform body copy, hook lines. Show Brayden for approval.
4. **Produce video assets**: feed demo footage into the clipping tool Brayden picked → export 9:16, 1:1, 16:9 variants → burn captions. Render Remotion hero sizzle. Preview before mass-uploading.
5. **Launch MCP**: `npx chrome-devtools-mcp@latest --autoConnect` using Brayden's logged-in Chrome profile.
6. **Auth check** (see Step 1 above): run the full login verification table. Halt on any `✗` until Brayden logs in manually.
7. **Post everywhere**, with 5–15min gaps on Reddit and 10–30min gaps on video platforms:
   - Navigate via typed address bar (not programmatic nav).
   - Screenshot → locate form controls visually.
   - Click title field → type title with human cadence.
   - Click body / upload video.
   - Screenshot to verify → click Submit.
   - Screenshot the result → save URL.
8. **Report back**: list every post URL with timestamp and platform. Note monitoring locations.

## Things that will get the account banned — don't

- Identical copy across many subreddits in a short window → spam filter. Rewrite per sub.
- Posting where the sub has karma requirements Brayden doesn't meet. Check sidebars.
- Same new domain in every post — Reddit shadowbans fresh domains. Alternate: direct link, Twitter thread, IH post, GitHub.
- More than ~5 posts/hr from a newish Twitter account → rate limit / suspension.
- LinkedIn auto-repost flows → write fresh each time.
- TikTok: same video reuploaded to multiple accounts → shadowban. One upload per account.

## If blocked

- Screenshot the block/captcha and show Brayden. **Do not attempt to solve captchas.**
- Reddit shadowban suspected? Tell him to check `reddit.com/r/ShadowBan`.
- Switch platforms and come back later. Do not hammer.

## Image generation via logged-in chat UIs (preferred method)

For OG cards, sizzle-frame plates, memes, thumbnails, and any other image asset — **prefer driving the web chat UIs of ChatGPT or Gemini via Chrome DevTools MCP over hitting paid APIs.** Brayden already pays for ChatGPT Plus and Gemini Advanced; use those subscriptions.

**Why this is better for creative work:**
- Free (already subscribed) vs. API spend.
- Iterative: type a prompt → screenshot the result → judge quality → type a follow-up ("move the logo left, make the lighting more cinematic, no gradients") → repeat until it's right. Much better than one-shot API prompts where a bad gen costs a full regeneration.
- Full context stays in the chat session — the model remembers the brand, earlier refinements, etc.

**Workflow:**
1. Navigate to `chatgpt.com` or `gemini.google.com` (whichever Brayden wants — ask in Step 0 if not specified).
2. Auth check: confirm logged in. Skip to the other provider if not.
3. Open image mode (ChatGPT: image upload / "/image" / whatever the current UI is; Gemini: image generation toggle).
4. Type the prompt (reference Brayden's brand spec from the Obsidian wiki if relevant — no gradients, no AI-slop look, project colors).
5. Wait for generation. Screenshot the result.
6. **Judge the image visually** against the brief. If it's off, type a specific refinement in the same chat. Do NOT start over unless the whole direction is wrong.
7. When approved, right-click the image → Save image as → save into a dedicated launch directory: `~/Desktop/Brayden's Projects/<project>/launch-assets/YYYY-MM-DD/` (create if missing). Name files semantically: `og-card-v3.png`, `sizzle-plate-02.png`, `meme-wallstreet.png`.
8. Report the file paths back to Brayden for his final approval before they're used in posts.

**Caveats:**
- Rate limits hit fast on both platforms. Don't try to bulk-generate 50 images in one go — batch small (5–10), pause, continue.
- ChatGPT / Gemini ToS technically cover automated use; keep volumes reasonable (this is human-pace iteration, not scraping).
- For true bulk or programmatic needs (e.g., 100 UTM-tagged thumbnails), fall back to API via `imagegen` / `mmx-cli` / Sora.

**When to NOT use this:**
- Video generation → use `/sora`, Runway, Pika, or `/mmx-cli` (web UIs for video are slower + more rate-limited).
- Precise layout/typography work (OG card with exact text placement) → use `frontend-design` or Remotion directly — chat UIs can't nail pixel-perfect text.

## Step 2 — Pre-post preflight (do once, right after auth check)

Before posting to anything, do these three preflight checks in parallel:

### 2a. Landing-page OG check

Fetch the target URL and look for:
- `<meta property="og:image">` — points to a real image, loads, roughly 1200×630
- `<meta property="og:title">` and `og:description`
- `<meta name="twitter:card">` (summary_large_image preferred)

If any are missing or broken:
1. Tell Brayden what's missing.
2. Offer to auto-generate a 1200×630 OG card. **Preferred path**: drive ChatGPT or Gemini image mode via Chrome DevTools MCP (see "Image generation via logged-in chat UIs" section above) — iterate in chat until it's right, save to the launch-assets folder. **Fallback**: `frontend-design` skill for pixel-perfect typography, or `imagegen` for one-shot.
3. If he approves, generate → he adds it to the landing page → re-verify → then proceed. Do not post until every major surface has a working preview card.

### 2b. UTM link registry

Before posting, generate a markdown table of every URL-to-be-used with UTM tags attached. Pattern: `?utm_source=<platform>&utm_medium=<sub-or-channel>&utm_campaign=<launch-slug>`.

Output the table in chat, e.g.:

```
| Platform     | Destination       | UTM-tagged URL |
|--------------|-------------------|----------------|
| r/algotrading| landing page      | https://.../?utm_source=reddit&utm_medium=algotrading&utm_campaign=openhedge-launch |
| Twitter thread | landing page    | https://.../?utm_source=twitter&utm_medium=thread&utm_campaign=openhedge-launch |
| HN           | landing page      | https://.../?utm_source=hn&utm_medium=show&utm_campaign=openhedge-launch |
| TikTok bio   | landing page      | https://.../?utm_source=tiktok&utm_medium=bio&utm_campaign=openhedge-launch |
| ...          | ...               | ... |
```

Keep this table — Brayden can reuse it for manual outreach (DMs, newsletters, etc.).

### 2c. Reddit per-sub gate check

For every target subreddit, navigate, screenshot the sidebar/rules, and extract:
- **Required post flair?** If yes, note which flair to pick.
- **Day-of-week restriction?** (e.g. "Self-promo only on Saturdays", "Show-off Sundays") — if today is not the allowed day, mark the sub as `DEFER` and don't post today.
- **Karma / account-age gate?** Screenshot the sidebar rule; cross-check Brayden's current karma/account age. If gated out, mark `SKIP` and note why.
- **Self-promotion cap?** (e.g. 9:1 rule) — if sub has a strict ratio and Brayden's history in the sub is thin, flag for him.
- **No-link rule?** Some subs ban outbound links entirely; those need a text post with the URL in a comment.

Output a preflight table:
```
| Sub           | Flair       | Day OK | Karma OK | Link OK | Status |
|---------------|-------------|--------|----------|---------|--------|
| r/algotrading | "Strategy"  | ✓      | ✓        | ✓       | GO     |
| r/quant       | none        | ✓      | ✓        | ✗ text  | GO (as text post) |
| r/SideProject | "Show"      | ✗ Sat  | ✓        | ✓       | DEFER to Saturday |
| r/MachineLearning | "[Project]" | ✓ | ✓     | ✓       | GO     |
```

Only posts marked `GO` proceed in this run. `DEFER` ones: use the `/schedule` skill to cron them for the right day. `SKIP` ones: drop them from the list.

## Additional high-leverage tactics (agent advice)

These are the things most AI-driven launches miss. Consider each — ask Brayden before acting on the ones marked **[ASK]**.

1. **Pre-flight landing page + email capture.** Before posting anywhere, verify the project has a real landing page with an email capture. Traffic to a repo with no capture leaks. If missing, stop and flag it.

2. **UTM-tag every outbound link.** Append `?utm_source=<platform>&utm_medium=<sub-or-channel>&utm_campaign=<launch-name>` to every link so Brayden can see what converted. Do this before posting, not after.

3. **Peak-hour scheduling, not fire-and-forget.** Stagger posts to each platform's actual peak:
   - **Hacker News**: Tue–Thu, 6–9am US Pacific — one shot only.
   - **Reddit**: depends on sub; generally 8–11am local time of the sub's dominant audience.
   - **LinkedIn**: Tue–Thu, 7–9am or 12–1pm local.
   - **TikTok / Reels**: 6–10pm local.
   - **Twitter**: 9–11am or 7–9pm ET for tech Twitter.
   Use the `schedule` skill if launching outside those windows.

4. **Social proof prime (first 60 min is everything).** Before pushing HN / Product Hunt, line up 3–5 genuine engaged commenters from Brayden's Discord / Slack / friends. **[ASK]** who to ping. Do NOT buy/trade upvotes — rings get detected and ban the post.

5. **Tell Brayden which posts will attract comments.** The agent's job ends when posting is done (post-and-forget). But flag which targets (HN, r/programming, top-tier subs) will likely get comments within the first 2h so he can choose to check in himself.

6. **Cross-link the graph.** Twitter thread links to HN post + YouTube. HN post's text links to Twitter. YouTube description lists every social. Creates trust signal and routes engagement.

7. **Newsletter submissions.** Identify 2–5 niche newsletters in the project's space and submit:
   - AI/LLM: TLDR AI, Ben's Bites, AlphaSignal, The Rundown
   - Dev tools: TLDR, Console.dev, Hacker Newsletter
   - Fintech/quant: **[ASK]** Brayden which he reads
   - Indie / SaaS: IndieHackers newsletter, SaaS Weekly, TheSaasNews

8. **Seeded influencer DMs (24–48h before public launch).** Identify 3–5 niche accounts (not celebs — mid-tier 10k–100k followers in the exact niche) and DM a preview with a "no pressure, share if interested" angle. **[ASK]** Brayden to approve the list and copy before sending.

9. **Live-stream the launch.** A 60–90min YouTube / Twitch / X live stream while the posts go out creates a "event" feel and gives people a place to gather. Optional but high-leverage.

10. **Second-wave content (days 2–7).** A launch isn't one post. Schedule:
    - Day 2: "Things I learned shipping X" post on Twitter + LinkedIn
    - Day 3: respond to top feedback → write a "what we're fixing" update
    - Day 5: first user/result spotlight
    - Day 7: "launch retro" — numbers, screenshots, what worked
    Keeps momentum alive after the day-1 spike.

11. **Meme-native content for meme-native audiences.** For r/wallstreetbets / AI Twitter / dev-humor subs, a single well-aimed meme beats 10 serious posts. Generate via `imagegen` skill or Photoshop manually.

12. **Press / journalist outreach.** For serious projects (OpenHedge, TrustBench), pitch 5 relevant journalists directly via email with a 60-second read: who you are, what you built, why now, what's unique, ask. **[ASK]** Brayden for the target outlet list.

13. **Waitlist / scarcity framing.** "First 100 users get X" / "closed beta — DM for access" converts 5–10× better than "try it free." Works for most of Brayden's projects.

14. **Record the launch day itself.** Screen + webcam capture during the launch run — makes raw material for future "how I launched" content.

15. **Open-source angle as a separate shot.** If the repo is public, do a dedicated Show HN / r/opensource / r/programming pass *just* for the OSS angle, distinct from the product launch.

16. **Post-mortem to the wiki.** After 48h, append a note to the Obsidian wiki entry for the project with: what posted where, what hit, what flopped, what to repeat next launch. Compounds over time.

17. **Do NOT use Buffer / Hootsuite / Typefully for Reddit / LinkedIn / TikTok.** They leave automation fingerprints and get flagged. Manual (via Chrome DevTools MCP + visual reasoning) is the whole point of this skill. Typefully/Hypefury are *acceptable* for Twitter threads since Twitter tolerates scheduling tools.

18. **Competitor piggyback.** Find a recent popular HN/Reddit thread about a competing/similar product, drop a substantive comment (not spam) with a link to Brayden's project as "related". Only if the comment genuinely adds value.

## Output format (end of run)

```
Launch report — <project name>

Reddit:
  - r/<sub1>: <url> — <time>
  - r/<sub2>: <url> — <time>
  - ...
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
Skipped: <platform> (<reason>)

Monitor: <where comments/DMs will arrive over the next 48h>
```
