# Anti-detection

Reddit, Twitter/X, LinkedIn, Product Hunt, TikTok, and Instagram fingerprint automation. Common tells:

- WebDriver / CDP flags in `navigator`
- DOM-level form fills (`input.value = "..."` or paste in one tick)
- Zero mouse movement, instant submit
- Headless Chrome signatures
- Canvas/WebGL fingerprint anomalies

**The fix is to behave like a human using their eyes.**

## Visual-reasoning workflow

1. **Screenshot** the current page (viewport or full page).
2. **Reason from the image**: "Create Post button is near top-right at roughly (x=1240, y=80). Title field is the large input in the center. Body editor is below."
3. **Act via real input events**: click coordinates, type characters one at a time with human-ish cadence (50–150ms per keystroke with jitter), scroll smoothly.
4. **Screenshot again** to verify state before the next action.
5. **Never** use `document.querySelector(...).value = "..."` to fill forms.
6. **Never** paste a giant text block in one keystroke — type it.

Detection systems hunt for DOM manipulation and millisecond-perfect behavior; visual-reasoning-driven input defeats most of them.

## Timing heuristics (tune, don't ignore)

- Page load → first click: **2–5s** (simulate reading)
- Between keystrokes: **50–150ms** with jitter (not fixed)
- Between actions (click → type → click): **500ms–2s**
- Before hitting Submit: re-read the post, pause **3–10s**
- Between posts on different subreddits: **5–15 minutes** — do not rapid-fire
- Between uploads on the same video platform: **10–30 minutes**

## Tab hygiene

See `browser-setup.md` — relevant only when using chrome-devtools-mcp against the user's real Chrome (gstack-browse runs in its own window, no concern).

## Capturing launch evidence

After every successful post, screenshot the live post (feed view + post-page view) and save to `~/Desktop/Brayden's Projects/<project>/launch-assets/YYYY-MM-DD/posts/<platform>-<sub>.png`.

For video: prompt user to QuickTime screen-record during the live run if the browser tool doesn't record natively. Raw `.png`/`.mov` stay in `launch-assets/`, git-ignored. These feed the next launch's video pipeline.

## If blocked

- Screenshot the block/captcha, show the user. **Do not attempt to solve captchas.**
- Reddit shadowban suspected? Tell user to check `reddit.com/r/ShadowBan`.
- Switch platforms and come back later. Do not hammer.

## Things that ban accounts — never do these

- Identical copy across many subreddits in a short window → spam filter. Rewrite per sub.
- Posting where the sub has karma gates the user doesn't meet.
- Same new domain in every post → Reddit shadowbans fresh domains. Alternate: direct link, Twitter thread, IH post, GitHub.
- More than ~5 posts/hr from a newish Twitter account → rate limit / suspension.
- LinkedIn auto-repost flows → write fresh each time.
- TikTok: same video reuploaded to multiple accounts → shadowban. One upload per account.
