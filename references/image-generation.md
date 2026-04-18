# Image generation

For OG cards, sizzle-frame plates, memes, thumbnails: **drive ChatGPT or Gemini image mode via the browser** instead of paid APIs. User already pays for ChatGPT Plus + Gemini Advanced — use those subscriptions.

## Why this beats API for creative work

- Free (already subscribed) vs. API spend.
- **Iterative**: prompt → screenshot result → judge → refine in the same chat ("move the logo left, more cinematic, no gradients") → repeat until right. Beats one-shot API prompts.
- Chat session keeps brand context across refinements.

## Workflow

1. Navigate to `chatgpt.com` or `gemini.google.com` (which one set in Step 0 intake).
2. Auth check — confirm logged in. Skip to the other if not.
3. Open image mode (ChatGPT's image upload / Gemini's generation toggle).
4. Prompt (reference brand spec from Obsidian wiki if relevant — no gradients, no AI-slop, project colors).
5. Wait for generation. Screenshot result.
6. Judge visually. If off, type a specific refinement in the SAME chat. Don't start over unless direction is wrong.
7. When approved: right-click image → Save image as → save to `~/Desktop/Brayden's Projects/<project>/launch-assets/YYYY-MM-DD/`. Name semantically: `og-card-v3.png`, `sizzle-plate-02.png`, `meme-wallstreet.png`.
8. Report file paths back for final approval before use.

## Caveats

- Rate limits hit fast. Batch 5–10, pause, continue.
- ChatGPT / Gemini ToS cover automated use; keep volumes human-pace, not scraping.
- For true bulk (100+ UTM-tagged thumbnails) → fall back to API via `imagegen` / `mmx-cli` / Sora.

## When NOT to use chat-UI image gen

- Video generation → use `/sora`, Runway, Pika, or `/mmx-cli`. Web UIs for video are slow + rate-limited.
- Precise layout/typography (OG card with exact text placement) → use `frontend-design` or Remotion directly. Chat UIs can't nail pixel-perfect text.
