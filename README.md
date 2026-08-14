# Pronunciation Caption Engine

A self-contained, single-file caption generator for TikTok/Shorts/Reels-style
pronunciation-teaching captions (IPA + word, cinematic stacked-line layout).

**Live demo (after enabling Pages):** `https://<your-username>.github.io/<repo-name>/`

## Usage

1. Open `index.html` (locally or via GitHub Pages).
2. Tap the **＋** button (or press `T`) and paste your transcript as
   `word /ipa/ word /ipa/ ...` — line breaks don't matter.
3. Click **Generate Captions**. The engine will:
   - auto-chunk into 5–9 word captions at punctuation boundaries
   - classify each word small / medium / large (stopwords → small,
     stress marks / syllable count / flapping / hard consonants → large)
   - assign a per-chunk autoplay duration
   - render it through the fixed "master style" renderer
4. Preview with Play / Next / Prev, adjust the speed and font-size sliders,
   toggle IPA visibility.
5. Click **Export JSON** to download the generated `captions` array if you
   want to hand-tune it — reopen the pencil (✎) icon / press `E` to paste
   edited JSON back in.
6. Press `F` (or the ⛶ button) for distraction-free fullscreen recording mode.

## Deploying to GitHub Pages

1. Push this repo to GitHub.
2. Settings → Pages → Source: **Deploy from a branch**, branch `main`, folder `/ (root)`.
3. Your engine will be live at `https://<your-username>.github.io/<repo-name>/`.

## Architecture

Everything lives in `index.html`:

- **Renderer** (`buildLinesStructural`, `buildCompositionDOM`, `fitComposition`,
  `goTo`) — the fixed visual "master style." Do not edit this to add features;
  it should stay identical across projects.
- **Transcript parser** (`parseTranscript`, `chunkWords`, `classifyChunk`,
  `buildCaptionsFromWords`) — the only part that changes per project. Takes
  `word /ipa/` pairs and produces the `captions` data array the renderer consumes.
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
