# Pronunciation Caption Engine

A self-contained, single-file caption generator for TikTok/Shorts/Reels-style
pronunciation-teaching captions (IPA + word, cinematic stacked-line layout).

**Live demo (after enabling Pages):** `https://<your-username>.github.io/<repo-name>/`

## Usage

1. Open `index.html` (locally or via GitHub Pages).
2. Tap the **＋** button (or press `T`) and paste your transcript as
   `word /ipa/ word /ipa/ ...` — line breaks don't matter.
3. Click **Generate Captions**. The engine will:
   - classify each word small / medium / large from its own text/IPA
     complexity (stopwords → small, stress marks / syllable count /
     flapping / hard consonants → large) — independent of chunk
     boundaries
   - dynamically group words into captions using the renderer's own
     line-layout rule, so a caption is only ever as long as it can
     stay compact (no fixed word count — simple content packs more
     words in, complex content splits sooner), preferring to cut on
     punctuation when there's already enough content
   - assign a per-chunk autoplay duration
   - render it through the fixed "master style" renderer, which
     independently computes the minimum scale a given caption genuinely
     needs (if any) — never inherited from the previous caption
4. Preview with Play / Next / Prev, adjust the speed and font-size sliders,
   toggle IPA visibility.
5. Click **Export JSON** to download the generated `captions` array if you
   want to hand-tune it — reopen the pencil (✎) icon / press `E` to paste
   edited JSON back in.
6. Press `F` (or the ⛶ button) for distraction-free fullscreen presentation mode.
7. Click **Start Recording** to capture just the caption preview (not the
   controls or browser chrome) as a downloadable `.webm` file — pick
   "This Tab" when the browser asks what to share, then **Stop Recording**
   when the sequence finishes.

## Deploying to GitHub Pages

1. Push this repo to GitHub.
2. Settings → Pages → Source: **Deploy from a branch**, branch `main`, folder `/ (root)`.
3. Your engine will be live at `https://<your-username>.github.io/<repo-name>/`.

## Architecture

Everything lives in `index.html`:

- **Renderer** (`buildLinesStructural`, `buildCompositionDOM`, `fitComposition`,
  `goTo`) — the fixed visual "master style." Do not edit this to add features;
  it should stay identical across projects. `goTo` is idempotent (safe to call
  repeatedly/rapidly — it always leaves exactly one composition on screen) and
  `fitComposition` computes each composition's own scale independently, with
  no state carried over from the previous caption.
- **Transcript parser** (`parseTranscript`, `classifyWord`, `chunkWords`,
  `buildCaptionsFromWords`) — the only part that changes per project. Takes
  `word /ipa/` pairs, classifies each word on its own merits, then dynamically
  groups words into captions (reusing the renderer's own line-layout rule —
  no fixed word count) to produce the `captions` data array the renderer
  consumes.
- **Recording** (`startRecording`, `stopRecording`) — captures just the
  `#app` preview region via `getDisplayMedia` → cropped `<canvas>` →
  `MediaRecorder`, downloading a `.webm` file. No external dependencies.
- **Data format**:
  ```json
  [
    { "duration": 2400, "words": [
      { "text": "Tonight", "ipa": "/təˈnaɪt/", "size": "large" }
    ]}
  ]
  ```

Because the renderer only ever reads the `captions` array, you can plug in
other sources later (SRT import, automatic transcription, audio sync) without
touching the visual code — just produce the same array shape.

## Keyboard shortcuts

| Key | Action |
|---|---|
| Space / ↓ | Next caption |
| ↑ | Previous caption |
| Home / End | First / last caption |
| F | Presentation (fullscreen recording) mode |
| Esc | Exit presentation mode |
| E | Open JSON edit mode |
| T | Open transcript import |

## License

Use freely for your own projects.
