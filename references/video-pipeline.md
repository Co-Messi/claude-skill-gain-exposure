# Video pipeline

Short-form video often out-reaches text by 10–100x. Budget ~**60 minutes** for a proper video lane.

Every launch should produce at least:
- **One hero sizzle** — 30–60s, cinematic/futuristic, programmatic (Remotion). For Twitter lead tweet, LinkedIn, YT Shorts, TikTok lead clip.
- **Short clips** — 4–8 clips, 15–45s, auto-cut from demo footage, 9:16 captioned. Flood TikTok / Reels / Shorts.
- **Long-form demo** — 3–10 min walkthrough on user's YouTube channel. Source for the shorts.

## Credentials — ASK, don't hardcode

Before picking tools, ask which the user wants. Never assume.

- **ElevenLabs** (TTS) — voice ID, model, API key location.
- **YouTube channel** — which channel, public / unlisted / scheduled?
- **Text-to-video** — Sora / Runway / Pika / MiniMax (`/mmx-cli`) / Veo / skip?
- **Clipping tool** — Opus Clip / Submagic / Klap / Vizard / Descript / CapCut?
- **Music library** — Epidemic / Artlist / Soundstripe / YouTube Audio / skip?

Phrase: "For this launch I need voiceover, music, and a cinematic intro. You have ElevenLabs — which voice? Sora or Runway for the intro? Which music library?"

## Track 1 — Hero sizzle (Remotion)

Use `remotion` / `remotion-video-creation` skills.

Aesthetic (no AI-slop, no gradients):
- Cinematic, futuristic, clean
- Typography-driven hero frames with project name + one-line promise
- Real screen capture composited into frame (laptop/phone mockups, parallax)
- Motion: subtle, high-end easing curves, not default spring bounces
- Color: pull from project's brand if documented in Obsidian wiki; else ask
- Duration: 30–60s

Audio:
- **Voiceover** via chosen TTS. Script 80–120 words for 45s. Render, play back for approval before cutting.
- **Music bed** — low-key cinematic synth or tech-modern, ducked under VO.
- **SFX** — subtle whooshes on title cards, light UI clicks on demo segments.
- **Mix** — VO at −3dB, music at −18dB under VO, back to −10dB in gaps.

## Track 2 — Short clips (flood)

Feed the long demo recording into the chosen clipping tool. Auto-cut 4–8 clips, 15–45s, 9:16, captions burned in. Rewrite caption per platform.

## Track 3 — Long-form demo (YouTube)

Full walkthrough, 3–10 min. Title + thumbnail matter — get user approval before upload. Add timestamps in description. Shorts + sizzle link back to this video.

## If no raw footage yet

1. Screen-record a clean demo (QuickTime or user's preferred recorder).
2. Optionally pair with webcam PiP (IndieHackers / LinkedIn credibility).
3. For the cinematic intro, generate plates via chosen text-to-video and composite inside the Remotion scene.

## Upload targets

- **TikTok** — 9:16, captions burned.
- **YouTube Shorts** — 9:16; link long video in description.
- **Instagram Reels** — 9:16.
- **Twitter/X** — hero sizzle at 1:1 or 16:9 in the launch thread's lead tweet.
- **LinkedIn** — hero sizzle at 1:1 with story caption.
- **YouTube long-form** — the full walkthrough.

Rewrite caption per platform. TikTok = punchy + trend-aware. LinkedIn = story-first. YouTube = SEO (project name + what it does + keywords).

## Timing expectation

~60 minutes real work:
- 15min: script + voiceover (ElevenLabs)
- 20min: Remotion hero build/render
- 10min: auto-clip demo into shorts
- 10min: upload long video + thumbnail
- 5min: upload shorts with per-platform captions

If the agent is producing AI-slop, STOP and ask user to approve style before continuing — bad video hurts the launch.
